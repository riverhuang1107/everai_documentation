# 使用公开卷搭建Stable Diffusion 1.5模型图片生成服务

在快速入门中，你已经创建了一个简单的应用。在这个示例中，我们使用公开卷中存放的`Stable Diffusion 1.5`模型文件，实现一个基于`Stable Diffusion 1.5`模型的图片生成在线服务。  

## 创建应用

首先，为你的应用创建一个目录，进入应用目录后，首先需要使用你从[EverAI](https://everai.expvent.com)获取到的token进行登录。登录成功后，使用`everai app create`命令创建你的应用。
```bash
everai login --token <your token>
```
## 创建密钥

如果你已经为你需要访问的镜像仓库创建过密钥，或者你的镜像仓库可以被公开访问，这一步是可选的。  

在这个例子中，我们会为[quay.io](https://quay.io/)创建一个密钥。
```bash
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```
>**小贴士**  
>
>[quay.io](https://quay.io/)是一个知名的公共镜像仓库，与之类似的知名镜像仓库还有[Docker Hub](https://hub.docker.com/)，[GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)，[Google Container Registry](https://cloud.google.com/artifact-registry)等。  

## 编写你的代码
### 基本设置

这是一个关于[app.py](https://github.com/everai-example/stable-diffusion-v1-5-with-public-volume/blob/main/app.py)的示例代码。  

首先，引入必要的EverAI Python类库。然后定义所需要用到的变量名，包括卷，访问镜像仓库的密钥，以及存放在卷中的文件等。使用`Image.from_registry`静态方法创建一个镜像实例。通过App类来创建定义一个app实例。  

这里需要注意的是，你需要为你的应用配置GPU资源，这里配置的GPU型号是"A100 40G"，GPU卡的数量是1。  

```python
from everai.app import App, context, VolumeRequest
from everai_autoscaler.builtin import SimpleAutoScaler
from everai.image import Image, BasicAuth
from everai.resource_requests import ResourceRequests
from everai.placeholder import Placeholder
from image_builder import IMAGE

APP_NAME = '<your app name>'
VOLUME_NAME = 'expvent/models--runwayml--stable-diffusion-v1-5'
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
        QUAY_IO_SECRET_NAME,
    ],
    configmap_requests=[CONFIGMAP_NAME],
    autoscaler=SimpleAutoScaler(
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

你可以使用我们提供的公开卷`expvent/models--runwayml--stable-diffusion-v1-5`中的模型文件加载模型。

```python
from diffusers import StableDiffusionPipeline, StableDiffusionImg2ImgPipeline
import torch

txt2img_pipe = None
img2img_pipe = None

@app.prepare()
def prepare_model():
    volume = context.get_volume(VOLUME_NAME)
    assert volume is not None and volume.ready

    secret = context.get_secret(HUGGINGFACE_SECRET_NAME)
    assert secret is not None
    huggingface_token = secret.get('token-key-as-your-wish')

    model_dir = volume.path

    global txt2img_pipe
    global img2img_pipe

    txt2img_pipe = StableDiffusionPipeline.from_pretrained(MODEL_NAME,
                                                        token=huggingface_token,
                                                        cache_dir=model_dir,
                                                        revision="fp16", 
                                                        torch_dtype=torch.float16, 
                                                        low_cpu_mem_usage=False,
                                                        local_files_only=True
                                                        )
    
    # The self.components property can be useful to run different pipelines with the same weights and configurations without reallocating additional memory.
    # If you just want to use img2img pipeline, you should use StableDiffusionImg2ImgPipeline.from_pretrained below.
    img2img_pipe = StableDiffusionImg2ImgPipeline(**txt2img_pipe.components)
    #image_pipe = StableDiffusionImg2ImgPipeline.from_pretrained(MODEL_NAME,
    #                                                            token=huggingface_token,
    #                                                            cache_dir=model_dir,
    #                                                            revision="fp16",
    #                                                            torch_dtype=torch.float16, 
    #                                                            low_cpu_mem_usage=False,
    #                                                            local_files_only=True
    #                                                            )
      
    if torch.cuda.is_available():
            txt2img_pipe.to("cuda")
            img2img_pipe.to("cuda")
    elif torch.backends.mps.is_available():
        mps_device = torch.device("mps")
        txt2img_pipe.to(mps_device)
        img2img_pipe.to(mps_device)
```
如果你想在本地使用`everai app run`调试这个示例，你的本地调试环境需要有GPU资源（英伟达GPU或者具有Metal编程框架的苹果GPU），并且在调试代码前使用`everai volume pull`命令把云端的模型文件拉取到本地环境。  

```bash
everai volume pull expvent/models--runwayml--stable-diffusion-v1-5
```

### 实现推理服务
#### 文生图
加载`Stable Diffusion 1.5`模型后，这里的代码使用了`flask`实现了文生图的推理在线服务。  
```python
import flask
from flask import Response

import io

image_pipe = None

# service entrypoint
# api service url looks https://everai.expvent.com/api/routes/v1/stable-diffusion-v1-5/txt2img
# for test local url is http://127.0.0.1:8866/txt2img
@app.service.route('/txt2img', methods=['GET','POST'])
def txt2img():    
    if flask.request.method == 'POST':
        data = flask.request.json
        prompt = data['prompt']
    else:
        prompt = flask.request.args["prompt"]

    pipe_out = txt2img_pipe(prompt)

    image_obj = pipe_out.images[0]

    byte_stream = io.BytesIO()
    image_obj.save(byte_stream, format="PNG")

    return Response(byte_stream.getvalue(), mimetype="image/png")
```
#### 图生图
这里的代码使用了`flask`实现了图生图的推理在线服务。  
```python
import PIL
from io import BytesIO

# service entrypoint
# api service url looks https://everai.expvent.com/api/routes/v1/stable-diffusion-v1-5/img2img
# for test local url is http://127.0.0.1:8866/img2img
@app.service.route('/img2img', methods=['POST'])
def img2img():        
    f = flask.request.files['file']
    img = f.read()

    prompt = flask.request.form['prompt']
    
    init_image = PIL.Image.open(BytesIO(img)).convert("RGB")
    init_image = init_image.resize((768, 512))

    pipe_out = img2img_pipe(prompt=prompt, image=init_image, strength=0.75, guidance_scale=7.5)

    image_obj = pipe_out.images[0]

    byte_stream = io.BytesIO()
    image_obj.save(byte_stream, format="JPEG")

    return Response(byte_stream.getvalue(), mimetype="image/jpg")
```

## 构建镜像

这步需要使用`Dockerfile`和`image_builder.py`来为你的应用构建容器镜像。  

这是一个关于[image_builder.py](https://github.com/everai-example/stable-diffusion-v1-5-with-public-volume/blob/main/image_builder.py)的示例代码。  

在`image_builder.py`中，你需要配置你的镜像地址信息。  

在这个示例中，我们选择使用[quay.io](https://quay.io/)作为公共镜像仓库，存放应用镜像。你也可以使用与之类似的知名镜像仓库，如：[Docker Hub](https://hub.docker.com/)，[GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)，[Google Container Registry](https://cloud.google.com/artifact-registry)等。如果你有自建的镜像仓库，并且镜像可以在互联网上被访问，同样可以使用。  

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
everai app create
```

执行`everai app list`后，可以看到类似如下的输出结果。如果你的应用状态是`DEPLOYED`，意味着你的应用已经部署成功。  
```bash
NAME                   NAMESPACE    STATUS    WORKERS    CREATED_AT
---------------------  -----------  --------  ---------  ------------------------
stable-diffusion-v1-5  default      DEPLOYED  1/1        2024-06-19T05:16:05+0000
```

当你看到你的应用处于`DEPLOYED`时，你可以使用`curl`执行下面的请求来测试你部署的文生图代码，在控制台上的当前目录下会产生一张由大模型`Stable Diffusion 1.5`生成的图片。 

```bash
curl -X POST -d '{"prompt": "a photo of a cat on the boat"}' -H 'Content-Type: application/json' -H'Authorization: Bearer <your_token>' -o test.png https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/txt2img
```

打开图片，可以看到如下效果。

<img src="https://expvent.com.cn:1111/evfiles/v1/expvent/public/everai-documentation/demo-cat.png" width = "512" /> 

此外，你还可以使用`curl`执行下面的请求来测试你部署的图生图代码。 

在使用`curl`请求之前，你需要先把`sketch-mountains-input.jpg`下载到你的本地目录下，在控制台终端的该目录下执行`curl`，会产生一张基于`sketch-mountains-input.jpg`这张原图和`prompt`由大模型`Stable Diffusion 1.5`生成的新的图片。

```bash
curl -X POST -F 'file=@sketch-mountains-input.jpg' -F "prompt=A fantasy landscape, trending on artstation" -H'Authorization: Bearer <your_token>' -o test.jpg https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/img2img
```

原图示例如下所示。    

<img src="https://expvent.com.cn:1111/evfiles/v1/expvent/public/everai-documentation/sketch-mountains-input.jpg" width = "512" />

打开基于原图和`prompt`生成的新图片，可以看到如下效果。

<img src="https://expvent.com.cn:1111/evfiles/v1/expvent/public/everai-documentation/img2img-output.jpg" width = "512" />
