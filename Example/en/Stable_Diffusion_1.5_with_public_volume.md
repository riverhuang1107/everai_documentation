# Stable Diffusion 1.5 with public volume
In the quickstart, you created a simple application. In this example, we use a public volume to store the `Stable Diffusion 1.5` model files and implement an AIGC(AI generated content) online service.    

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

APP_NAME = '<your app name>'
VOLUME_NAME = 'expvent/stable-diffusion-v1-5'
QUAY_IO_SECRET_NAME = 'your-quay-io-secret-name'

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
    autoscaling_policy=SimpleAutoScalingPolicy(
        # keep running workers even no any requests, that make reaction immediately for new request
        min_workers=1,
        # the maximum works setting, protect your application avoid to pay a lot of money
        # when an attack or sudden traffic
        max_workers=10,
        # this factor control autoscaler how to scale up your app
        max_queue_size=3,
        # this factor control autoscaler how to scale down your app
        max_idle_time=60,
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

You can load the model using the model file in the public volume `expvent/stable-diffusion-v1-5` we provide.  

```python
@app.prepare()
def prepare_model():
    volume = context.get_volume(VOLUME_NAME)
    assert volume is not None and volume.ready

    model_dir = volume.path

    global image_pipe

    #image_pipe = StableDiffusionPipeline.from_pretrained("runwayml/stable-diffusion-v1-5")
    image_pipe = StableDiffusionPipeline.from_pretrained(model_dir,
                                                        local_files_only=True,
                                                        revision="fp16", 
                                                        torch_dtype=torch.float16, 
                                                        low_cpu_mem_usage=False
                                                        )
    image_pipe.to("cuda")
```
If you want to use `everai app run` to debug this example locally, your local debugging environment needs to have GPU resources, and use `everai volume pull` command to pull the model file from the cloud to the local environment before debugging the code.  

```bash
everai volume pull expvent/stable-diffusion-v1-5
```

### Generate inference service
Aftering loading `Stable Diffusion 1.5` model, now you can write your Python code that uses `flask` to implement the inference online service of AIGC(AI generated content).  

```python
from diffusers import StableDiffusionPipeline
import torch

import flask
from flask import Response

import io

image_pipe = None

# service entrypoint
# api service url looks https://everai.expvent.com/api/apps/v1/routes/stable-diffusion-v1-5/txt2img
# for test local url is http://127.0.0.1:8866/txt2img
@app.service.route('/txt2img', methods=['POST'])
def txt2img():    
    data = flask.request.json
    prompt = data['prompt']

    pipe_out = image_pipe(prompt)

    image_obj = pipe_out.images[0]

    byte_stream = io.BytesIO()
    image_obj.save(byte_stream, format="PNG")

    return Response(byte_stream.getvalue(), mimetype="image/png")
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
NAME                         STATUS     CREATED_AT                ROUTE_NAME
---------------------------  ---------  ------------------------  ---------------------------
stable-diffusion-v1-5        DEPLOYED   2024-05-19 18:47:32+0800  stable-diffusion-v1-5
```
When your app is deployed, you can use `curl` to execute the following request to test your deployed code, A picture generated by the `Stable Diffusion 1.5` model will be downloaded in the current directory on the console.  

```bash
curl -X POST -d '{"prompt": "a photo of a dog on the boat"}' -H 'Conte
nt-Type: application/json' -H'Authorization: Bearer <your_token>' -o test.png https://everai.expvent.com/api/apps/v1/routes/<your app route name>/txt2img
```

Open the picture and you can see the following effect.  

<img src="https://expvent.com.cn:1111/evfiles/v1/expvent/public/everai-documentation/demo-cat.png" width = "512" />






