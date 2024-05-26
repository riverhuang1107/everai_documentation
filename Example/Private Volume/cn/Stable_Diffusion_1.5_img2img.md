# Stable Diffusion 1.5: img2img with private volume

在快速入门中，你已经创建了一个简单的应用。在这个示例中，我们使用私有卷存放`Stable Diffusion 1.5`模型文件，实现一个基于`Stable Diffusion 1.5`模型的图生图在线服务。  

## 创建应用

首先，为你的应用创建一个目录，进入应用目录后，首先需要使用你从[EverAI](https://everai.expvent.com)获取到的token进行登录。登录成功后，使用`everai app create`命令创建你的应用。
```bash
everai login --token <your token>

everai app create <your app name>
```
## 创建密钥

如果你已经为你需要访问的镜像仓库创建过密钥，这一步可以跳过。  

在这个例子中，我们会为[quay.io](https://quay.io/)创建一个密钥。
```bash
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```

## 编写你的代码
### 基本设置

这是一个关于app.py的示例代码。  

首先，引入必要的EverAI Python类库。然后定义所需要用到的变量名，包括卷，访问镜像仓库的密钥，以及存放在卷中的文件等。使用`Image.from_registry`静态方法创建一个镜像实例。通过App类来创建定义一个app实例。  

这里需要注意的是，你需要为你的应用配置GPU资源，这里配置的GPU型号是"A100 40G"，GPU卡的数量是1。  

```python
from everai.app import App, context, VolumeRequest
from everai.autoscaling import SimpleAutoScalingPolicy
from everai.image import Image, BasicAuth
from everai.resource_requests import ResourceRequests
from everai.placeholder import Placeholder
from image_builder import IMAGE

APP_NAME = 'stable-diffusion-v1-5-img2img'
VOLUME_NAME = 'models--runwayml--stable-diffusion-v1-5'
QUAY_IO_SECRET_NAME = 'your-quay-io-secret-name'
HUGGINGFACE_SECRET_NAME = 'your-huggingface-secret-name'
MODEL_NAME = 'runwayml/stable-diffusion-v1-5'
CONFIGMAP_NAME = 'sd15-configmap'

image = Image.from_registry(IMAGE, auth=BasicAuth(
        username=Placeholder(QUAY_IO_SECRET_NAME, 'username', kind='Secret'),
        password=Placeholder(QUAY_IO_SECRET_NAME, 'password', kind='Secret'),
    ))
    
app = App(
    APP_NAME,
    image=image,
    volume_requests=[
        VolumeRequest(name=VOLUME_NAME, create_if_not_exists=True),
    ],
    secret_requests=[
        HUGGINGFACE_SECRET_NAME,
        QUAY_IO_SECRET_NAME
    ],
    autoscaling_policy=SimpleAutoScalingPolicy(
        # keep running workers even no any requests, that make reaction immediately for new request
        min_workers=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='min_workers'),
        # the maximum works setting, protect your application avoid to pay a lot of money
        # when an attack or sudden traffic
        max_workers=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='max_workers'),
        # this factor controls autoscaler how to scale up your app
        max_queue_size=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='max_queue_size'),
        # this factor controls autoscaler how to scale down your app
        max_idle_time=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='max_idle_time'),
        # this factor controls autoscaler how many steps to scale up your app from queue 
        scale_up_step=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='scale_up_step'),
    ),
    resource_requests=ResourceRequests(
        cpu_num=2,
        memory_mb=20480,
        gpu_num=1,
        gpu_constraints=[
            "A100 40G"
        ],
    ),
)
```

### 预加载模型

如果你的本地环境没有模型文件，你可以使用`StableDiffusionImg2ImgPipeline.from_pretrained`方法传入`MODEL_NAME`，从[Hugging Face](https://huggingface.co/)官网拉取模型文件。并且会通过设置`cache_dir`，把模型文件缓存到私有卷`models--runwayml--stable-diffusion-v1-5`中。  

你可以通过`everai volume get`命令获取到卷`models--runwayml--stable-diffusion-v1-5`的本地路径。进入卷的本地路径后，可以看到已经被缓存的模型文件。  

```bash
everai volume get models--runwayml--stable-diffusion-v1-5
<Volume: id: iRizusPqYZsqPPNLSTnogW, name: stable-diffusion-v1-5, revision: 000001-e72, files: 19, size: 10.22 GiB>
path: /root/.cache/everai/volumes/iRizusPqYZsqPPNLSTnogW
```
使用`everai app run`调试示例代码时，`is_prepare_mode`的值是`False`，不会执行把本地文件推送到云端的操作。待你的代码调试通过后，执行`everai app prepare`命令，该命令会执行所有被`@app.prepare`注解过的方法，此时`is_prepare_mode`的值是`True`，在示例代码中，本地卷`stable-diffusion-v1-5`中的模型文件会在执行该命令时被推送到云端。

```python
@app.prepare()
def prepare_model():
    volume = context.get_volume(VOLUME_NAME)
    assert volume is not None and volume.ready

    secret = context.get_secret(HUGGINGFACE_SECRET_NAME)
    assert secret is not None
    huggingface_token = secret.get('token-key-as-your-wish')

    model_dir = volume.path

    global image_pipe

    image_pipe = StableDiffusionImg2ImgPipeline.from_pretrained(MODEL_NAME,
                                                     token=huggingface_token,
                                                     cache_dir=model_dir,
                                                     revision="fp16",
                                                     torch_dtype=torch.float16, 
                                                     low_cpu_mem_usage=False
                                                     )   
    # only in prepare mode push volume
    # to save gpu time (redundant sha256 checks)
    if context.is_prepare_mode:
        context.volume_manager.push(VOLUME_NAME)
    else:
        image_pipe.to("cuda")
```

### 实现推理服务

加载`Stable Diffusion 1.5`模型后，这里的代码使用了`flask`实现了图生图的推理在线服务。  
```python
from diffusers import StableDiffusionImg2ImgPipeline
import torch

import flask
from flask import Response

import io
import PIL
from io import BytesIO

image_pipe = None

# service entrypoint
# api service url looks https://everai.expvent.com/api/routes/v1/stable-diffusion-v1-5-img2img/img2img
# for test local url is http://127.0.0.1:8866/img2img
@app.service.route('/img2img', methods=['POST'])
def img2img():        
    f = flask.request.files['file']
    img = f.read()

    prompt = flask.request.form['text_field']
    prompt = prompt.split(':')[1]
    
    init_image = PIL.Image.open(BytesIO(img)).convert("RGB")
    init_image = init_image.resize((768, 512))

    pipe_out = image_pipe(prompt=prompt, image=init_image, strength=0.75, guidance_scale=7.5)

    image_obj = pipe_out.images[0]

    byte_stream = io.BytesIO()
    image_obj.save(byte_stream, format="JPEG")

    return Response(byte_stream.getvalue(), mimetype="image/jpg")
```

## 构建镜像

这步需要使用`Dockerfile`和`image_builder.py`来为你的应用构建容器镜像。  

在`image_builder.py`中，你需要配置你的镜像地址信息。
```python
from everai.image import Builder

IMAGE = 'quay.io/<username>/<repo>:<tag>'
```

首先确保你的docker环境处于登录状态，以及你已经安装了docker buildx插件。  
```bash
docker login quay.io
docker buildx version
```

然后执行以下命令打包镜像，并且把打包好的镜像推送到你指定的镜像仓库中。  
```bash
everai image build
```

## 部署

最后一步是把你的应用部署到EverAI。并使它保持在运行状态。  
```bash
everai app deploy
```

执行`everai app list`后，可以看到类似如下的输出结果。如果你的应用状态是`DEPLOYED`，意味着你的应用已经部署成功。  
```bash
NAME                         STATUS     CREATED_AT                ROUTE_NAME
---------------------------  ---------  ------------------------  ---------------------------
stable-diffusion-v1-5        DEPLOYED   2024-05-19 18:47:32+0800  stable-diffusion-v1-5
```

当你看到你的应用处于`DEPLOYED`时，你可以使用`curl`执行下面的请求来测试你部署的代码。 

在使用`curl`请求之前，你需要先把`sketch-mountains-input.jpg`下载到你的本地目录下，在控制台终端的该目录下执行`curl`，下会产生一张基于`sketch-mountains-input.jpg`这张原图和`prompt`由大模型`Stable Diffusion 1.5`生成的新的图片。

```bash
curl -X POST -F 'file=@sketch-mountains-input.jpg' -F "text_field=prompt:A fantasy landscape, trending on artstation" -H'Authorization: Bearer everai_637wE9obZtmGLyqIJp0lok' -o test.jpg https://everai.expvent.com/api/routes/v1/<your app route name>/img2img
```

原图示例如下所示。    

<img src="img/sketch-mountains-input.jpg" width = "512" />

打开基于原图和prompt生成的新图片，可以看到如下效果。

<img src="img/test.jpg" width = "512" />





