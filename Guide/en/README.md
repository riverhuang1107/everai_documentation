---
sidebar_position: 3
---

# Getting started
## Introduction
EverAI's platform empowers data/AI/ML teams to develop faster at lower cost, while scaling production workloads to thousands of CPUs and GPUs.

Run generative AI models, large-scale batch jobs, job queues, and much more.  

The EverAI CLI is a command-line interface for EverAI. It manages your resources including apps, secrets, volumes according to CLI interface.  

The EverAI Python library provides convenient, on-demand access to serverless cloud compute from Python scripts on your local computer.  

## Features
* **Pay as you go**  
You always pay for what you use and nothing more. You never pay for idle resources — just actual compute time. Only pay by the second.  

* **Focus on your code**  
Deploy your apps as serverless REST APIs, or an web endpoint, all with just a single line of code. Don't focus on the hardware infrastructure. We run the infrastructure.  

* **Provide multiple models of GPUs**  
You can choose the suitable GPU model according to your app workload. Deploy autoscaling inference endpoints on GPUs(4090s, A100s), when your app gets an influx of traffic.  

* **Develop locally on remote hardware**  
You can write code on your laptop and execute it on cloud hardware immediately, with lightning fast build times. Run AI models in the cloud scalably and performantly.  

* **Store large files in Storage Volumes**  
Read and write data to highly-performant distributed file systems.  

## Prerequisites
### Python
EverAI CLI library requires Python 3.10+.  

### EverAI
To continue with this quick start, you'll need access to the following from [EverAI](https://everai.expvent.com):  
* **EverAI account**  

  Create an account at [EverAI](https://everai.expvent.com).  

* **EverAI Token**  

  Get your account Token from [EverAI](https://everai.expvent.com).  

### Docker
To build your Docker image, you'll need access to the following:  
* **Docker installed**  

* **Docker Buildx installed**  

* **Docker account**  

>**NOTE**  
>
>Buildx could be used to build multiple-platform(x86, arm64) Docker Image.  

## Install, Upgrade and Uninstall
### Install
Install the package with `pip`. Run this in order to install the Python library locally.   
#### Linux(WSL) & MacOS  
```bash
pip install everai
```

#### Windows PowerShell  
Open PowerShell as Administrator:  
```bash
pip install everai --user
```

>**NOTE**  
>  
>If you installed EverAI CLI but you’re seeing an error like `everai: command not found` when trying to run the CLI, this means that the installation location of Python package executables (“binaries”) are not present on your system path. This is a common problem; you need to reconfigure your system’s environment variables to fix it.  
>* **Linux(WSL)**  
>```bash
>python3 -m site --user-base
>```
>
>We know that the scripts go to the `bin/` folder where the packages are installed. So we concatenate the paths with `bin/`. Finally, in order to avoid repeating the `export` command we add it to our `.bashrc` file and we run `source` to run the new changes.  
>```bash
>export PATH="/home/<username>/.local/bin:$PATH"
>```
>* **Windows PowerShell**  
>```bash
>python -m site --user-site
>```
>
>you can find the user base binary directory and replacing `site-packages` with `Scripts`. For example, this could return 			`C:\Users\<Username>\AppData\Roaming\Python\Python311\site-packages` so you would need to set your PATH to include `C:\Users\<Username>\AppData\Roaming\Python\Python311\Scripts`. You can set your user PATH permanently in the Control Panel. You may need to log out for the PATH changes to take effect.  

### Upgrade
Upgrade the package with `pip`(Linux, WSL, macOS, Windows PowerShell):  
```bash
pip install --upgrade everai
```
### Uninstall
Uninstall the package with `pip`(Linux, WSL, macOS, Windows PowerShell):  
```bash
pip uninstall everai
```

## Create an app directory
### File and directory structure
Before creating app In [EverAI](https://everai.expvent.com), firstly you should create a directory which is named by your app name generally. A EverAI app must includes the following four files at least:  

* **`Dockerfile`** This is the text file which is used to build docker image, and it includes series of instructions and commands used to build image.  

* **`app.py`** This is the code file which mainly provides your app's external service.  

* **`image_builder.py`** This is the config file that is used to build docker image.

* **`requirements.txt`** This file defines the dependent packages and corresponding software versions for running Python application. You can use `pipreqs .` to obtain the latest software dependency packages and corresponding versions.

```bash
<your app name>
├── Dockerfile
├── app.py
├── image_builder.py
└── requirements.txt
```

### Login EVERAI CLI
In your app directory, you should login by token you got in [EverAI](https://everai.expvent.com). After login successfully, run command `everai app create` to create your app.  
```bash
everai login --token <your token>
```

## Create secrets
Secrets are a secure way to add credentials and other sensitive information to the containers your functions run in.  

This step is optional, depending on whether the model and Docker image require security certification.  

You can create and edit secrets on [EverAI](https://everai.expvent.com), or programmatically from Python code.  

In this case, we will create one secret for [quay.io](https://quay.io/). 
```bash
everai secret create quay-secret \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```
>**NOTE**
>
>[quay.io](https://quay.io/) is a well-known public image registry. Well-known image registry similar to [quay.io](https://quay.io/) include [Docker Hub](https://hub.docker.com/), [GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry), [Google Container Registry](https://cloud.google.com/artifact-registry), etc.  

## Write your app code in python
### Basic setup
There is an example code in [app.py](https://github.com/everai-example/get-start/blob/main/app.py).  

First, import the required EverAI Python class library. Then define the variable names that need to be used, including the volume, the secret that accesses the image registry, and the file stored in the volume. Use the `Image.from_registry` static method to create a image instance. Create and define an app instance through the App class.  

```python
import everai.utils.cmd
from everai.app import App, context, VolumeRequest
from everai.image import Image, BasicAuth
from everai.resource_requests import ResourceRequests
from everai.placeholder import Placeholder
from image_builder import IMAGE

VOLUME_NAME = 'get-start-volume'
QUAY_IO_SECRET_NAME = 'quay-secret'
MODEL_FILE_NAME = 'my-model'

image = Image.from_registry(IMAGE, auth=BasicAuth(
        username=Placeholder(QUAY_IO_SECRET_NAME, 'username', kind='Secret'),
        password=Placeholder(QUAY_IO_SECRET_NAME, 'password', kind='Secret'),
    ))

app = App(
    '<your app name>',
    image=image,
    volume_requests=[
        VolumeRequest(name=VOLUME_NAME),
    ],
    secret_requests=[QUAY_IO_SECRET_NAME],
    resource_requests=ResourceRequests(
        cpu_num=1,
        memory_mb=1024,
    ),
)
```

### Prepare volume
Before your application is deployed in the cloud, you should construct your volume first, if your app uses at least one volume.  

You can create a function by the `@app.prepare` decorator to manager and prepare your volume and related files.  

```python
import os
import time
from everai.app import App, context, VolumeRequest

@app.prepare()
def prepare_model():
    volume = context.get_volume(VOLUME_NAME)

    model_path = os.path.join(volume.path, MODEL_FILE_NAME)
    if not os.path.exists(model_path):
        # download model file
        with open(model_path, 'wb') as f:
            f.write('hello world'.encode('utf-8'))

    # only in prepare mode push volume
    # to save gpu time (redundant sha256 checks)
    if context.is_prepare_mode:
        context.volume_manager.push(VOLUME_NAME)

    time.sleep(5)
```

For production environment, the volumes are very important, you could call the following command to prepare it.  

```bash
everai app prepare
```

This command line will call all functions which are decorated by `@app.prepare`, in these functions you should set up volume data before the app use it.  

In this example code, `my-model` file of volume `get-start-volume` in the local environment will be pushed to the cloud, when you run `everai app prepare`.  

### Generate API endpoint service

Now you can write your own Python code to implement the API endpoint service. The example here uses `flask` to implement an external service that reads the file `my-model` information in the volume `get-start-volume`.  

```python
import flask

# curl http://localhost/show-volume
# curl http://127.0.0.1/show-volume
# curl http://<your ip>/show-volume
@app.service.route('/show-volume', methods=['GET'])
def show_volume():
    volume = context.get_volume(VOLUME_NAME)
    model_path = os.path.join(volume.path, MODEL_FILE_NAME)
    with open(model_path, 'r') as f:
        return f.read()
```

Now，you could test in your local machine will following command.  

```bash
everai app run
```
You can use `curl` to request this API endpoint service and see `hello world` displayed on your terminal. This information is written to the file `my-model` when performing the previous step of `prepare_model`.  

```bash
curl http://<your ip>/show-volume
```

In addition, in the same application, you can implement multiple API endpoint services. This example uses `flask` to implement a external web service that sends active messages from the server to the client.  

```python
# curl --no-buffer http://localhost/sse
# curl --no-buffer http://127.0.0.1/sse
# curl --no-buffer http://<your ip>/sse
@app.service.route('/sse', methods=['GET'])
def sse():
    def generator():
        for i in range(10):
            yield f"hello again {i}\n\n"
            time.sleep(1)

    return flask.Response(generator(), mimetype='text/event-stream')
```

You can execute `everai app run` again, serving this web endpoint and hit it with `curl`, you will see the ten `SSE`(Server-Sent Events) events progressively appear in your terminal over a 10 second period.  

```bash
curl --no-buffer http://<your ip>/sse
```

## Build image
This step will build the container image, using two very simple files `Dockerfile` and `image_builder.py`.  

In `image_builder.py`, you should set your image repo.  

In this example, we choose to use [quay.io](https://quay.io/) as the public image registry to store application images. You can also use well-known image registry similar to [quay.io](https://quay.io/), such as [Docker Hub](https://hub.docker.com/), [GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry), [Google Container Registry](https://cloud.google.com/artifact-registry), etc. If you have a self-built image registry and the image can be accessed on the Internet, you can also use it.  

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
The final step is to deploy your app to [EverAI](https://everai.expvent.com) and keep it running.
```bash
everai app create  
```
After running `everai app list`, you can see the result similar to the following. If your app's status is `DEPLOYED`, it means that your app is deployed successfully.   
```bash
NAME       NAMESPACE    STATUS    WORKERS    CREATED_AT
---------  -----------  --------  ---------  ------------------------
get-start  default      DEPLOYED  3/3        2024-06-18T04:21:37+0000
```
Now, you can make a test call for your app, in these examples looks like:  
```bash
curl --no-buffer -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app route name>/sse
```
## One more thing
So far, you have created a simple application. But when the load of this application exceeds the endurance range of computing resources, the application's service response will slow down, time out, or even be unavailable. The [EverAI](https://everai.expvent.com) platform provides an autoscaling mechanism that can help your application automatically expand under high load conditions, eliminating the need for you to manually deploy new computing nodes.  For detailed guidance, check out [App Autoscaling](https://expvent.com/documentation/docs/Guide/App_Autoscaling).  

## License
The MIT License.  

## Contributing
