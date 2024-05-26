# Stable Diffusion 1.5: img2img with private volume
In the quickstart, you created a simple application. In this example, we use a private volume to store the `Stable Diffusion 1.5` model files and implement an AIGC(AI generated content) online image-to-image service.  

## Create an app
Create a directory for your app firstly. In your app directory, you should login by token you got in [EverAI](https://everai.expvent.com). After login successfully, run command `everai app create` to create your app.  

```bash
everai login --token <your token>

everai app create <your app name>
```
## Create secret
If you have already created a secret for your registry, you can skip this step.

In this case, we will create one secret for [quay.io](https://quay.io/).  

```bash
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```

## Write your app code in python
### Basic setup
There is an example code in app.py.  

First, import the required EverAI Python class library. Then define the variable names that need to be used, including the volume, the secret that accesses the image registry, and the file stored in the volume. Use the `Image.from_registry` static method to create a image instance. Create and define an app instance through the App class.  

What needs to be noted here is that you need to configure GPU resources for your application. The GPU model configured here is "A100 40G", and the number of GPU cards is 1.  

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

### Load model

If your local environment does not have a model file, you can use the `StableDiffusionImg2ImgPipeline.from_pretrained` method to pass in `MODEL_NAME` and pull the model file from the [Hugging Face](https://huggingface.co/) official website. And by setting `cache_dir`, the model file will be cached in the private volume `models--runwayml--stable-diffusion-v1-5`.  

You can get the local path of the volume `models--runwayml--stable-diffusion-v1-5` through the `everai volume get` command. After entering the local path of the volume, you can see the model files that have been cached.

```bash
everai volume get models--runwayml--stable-diffusion-v1-5
<Volume: id: Xo6zoFc4986CrD7dYuNrwr, name: models--runwayml--stable-diffusion-v1-5, revision: 000001-12d, files: 76, size: 10.21 GiB>
path: /root/.cache/everai/volumes/Xo6zoFc4986CrD7dYuNrwr
```
When using `everai app run` to debug the sample code, the value of `is_prepare_mode` is `False`, and the operation of pushing local files to the cloud will not be performed. After your code is debugged, execute the `everai app prepare` command. This command will execute all methods annotated by `@app.prepare`. At this time, the value of `is_prepare_mode` is `True`. In the sample code, the model files in the local volume `stable-diffusion-v1-5` will be pushed to the cloud when this command is executed.   

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

### Generate inference service
Aftering loading `Stable Diffusion 1.5` model, now you can write your Python code that uses `flask` to implement the inference online image-to-image service of AIGC(AI generated content).  

```python
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

## Build image
This step will build the container image, using two very simple files `Dockerfile` and `image_builder.py`.  

In `image_builder.py`, you should set your image repo.  

```python
from everai.image import Builder

IMAGE = 'quay.io/<username>/<repo>:<tag>'
```
The dependence of this step is docker and buildx installed on your machine. Otherwise we will have further prompts to help you install them.  

```bash
docker login quay.io
docker buildx version
```

Then call the following command will compile the image and push them to your specified registry.  

```bash
everai image build
```

## Deploy image
The final step is to deploy your app to everai and keep it running.  

```bash
everai app deploy
```
After running `everai app list`, you can see the result similar to the following. If your app's status is `DEPLOYED`, it means that your app is deployed successfully.  

```bash
NAME                           STATUS     CREATED_AT                ROUTE_NAME
-----------------------------  ---------  ------------------------  -----------------------------
stable-diffusion-v1-5-img2img  DEPLOYED   2024-05-26 10:23:13+0800  stable-diffusion-v1-5-img2img
```
When your app is deployed, you can use `curl` to execute the following request to test your deployed code.  

Before using `curl` to request, you need to download `sketch-mountains-input.jpg` to your local directory, execute `curl` in the directory of the console terminal, and a new image based on this original image of `sketch-mountains-input.jpg` and `prompt` will be generated by the large model `Stable Diffusion 1.5`.  


```bash
curl -X POST -F 'file=@sketch-mountains-input.jpg' -F "text_field=prompt:A fantasy landscape, trending on artstation" -H'Authorization: Bearer everai_637wE9obZtmGLyqIJp0lok' -o test.jpg https://everai.expvent.com/api/routes/v1/<your app route name>/img2img
```

An example of the original image is shown below.  

<img src="img/sketch-mountains-input.jpg" width = "512" />

Open the new picture and you can see the following effect.  

<img src="img/test.jpg" width = "512" />
