# Hello World!

## Install python client

EverAI CLI library requires Python 3.10+.  

Install the package with `pip`. Run this in order to install the Python library locally.   
```bash
pip install everai
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
In your app directory, you should login by token you got in [EverAI](https://everai.expvent.com).  

```bash
everai login --token <your token>
```

## Write your app code in python
### Basic setup
There is an example code in [app.py](https://github.com/everai-example/get-start/blob/main/app.py).  

Firstly, import the required EverAI Python class library. Use the `Image.from_registry` static method to create a image instance. Create and define an app instance through the App class.  

```python
from everai.app import App
from everai.image import Image
from everai.resource_requests import ResourceRequests
from image_builder import IMAGE

APP_NAME = '<your app name>'

image = Image.from_registry(IMAGE)

app = App(
    APP_NAME,
    image=image,
    resource_requests=ResourceRequests(
        cpu_num=1,
        memory_mb=1024,
    )
)
```

### Generate API endpoint service

Now you can write your own Python code to implement the API endpoint service. The example here uses `flask` to implement an external service.  

```python
# curl http://localhost:80/show
@app.service.route('/show', methods=['GET'])
def show():
    resp = 'hello world!'
    return resp
```

Now，you could test in your local machine will following command.  

```bash
everai app run
```
You can use `curl` to request this API endpoint service and see `hello world!` displayed on your terminal. 

```bash
curl http://<your ip>/show
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
everai -v image build
```

## Deploy image
The final step is to deploy your app to [EverAI](https://everai.expvent.com) and keep it running.
```bash
everai app create  
```
After running `everai app list`, you can see the result similar to the following. If your app's status is `DEPLOYED`, it means that your app is deployed successfully.   
```bash
NAME                             NAMESPACE    STATUS    WORKERS    CREATED_AT
-------------------------------  -----------  --------  ---------  ------------------------
get-start                        default      DEPLOYED  1/1        2024-07-10T05:38:24+0000
```
Now, you can make a test call for your app, in these examples looks like:  
```bash
curl -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/show
```