# README
## Introduction
EverAI's platform empowers data/AI/ML teams to develop faster at lower cost, while scaling production workloads to thousands of CPUs and GPUs.

Run generative AI models, large-scale batch jobs, job queues, and much more.  

The EverAI Python library provides convenient, on-demand access to serverless cloud compute from Python scripts on your local computer.  

## Features
* **Pay as you go**  
You always pay for what you use and nothing more. You never pay for idle resources — just actual compute time. Only pay by the hour.  

* **Focus on your code**  
Deploy your apps as serverless REST APIs, or an web endpoint, all with just a single line of code. Don't focus on the hardware infrastructure. We run the infrastructure.  

* **Provide multiple models of GPUs**  
You can choose the suitable GPU model according to your app workload. Deploy autoscaling inference endpoints on GPUs(4090s, A100s, L40s), when your app gets an influx of traffic.  

* **Develop locally on remote hardware**  
You can write code on your laptop and execute it on cloud hardware immediately, with lightning fast build times. Run AI models in the cloud scalably and performantly.  

* **Store large files in Storage Volumes**  
Read and write data to highly-performant distributed file systems.  

## Prerequisites
### Python
EverAI CLI library requires Python 3.8+.  

### EverAI
To continue with this quick start, you'll need access to the following from [EverAI](everai.expvent.com):  
* **EverAI account**  

  Create an account at [EverAI](everai.expvent.com).  

* **EverAI Token**  

  Get your account Token from [EverAI](everai.expvent.com).  

### Docker
To build your Docker image, you'll need access to the following:  
* **Docker installed**  

* **Docker Buildx installed**  

* **Docker account**  

>**Tips**  
>Buildx could be used to build multiple-platform(x86, arm64) Docker Image.  

## Quick start
### Install, Upgrade and Uninstall
#### Install
Install the package with `pip`. Run this in order to install the Python library locally.   
##### Linux(WSL) & MacOS  
```bash
pip install everai
```

##### Windows PowerShell  
Open PowerShell as Administrator:  
```bash
pip install everai --user
```

>**Tips**  
>If you installed EverAI CLI but you’re seeing an error like `everai: command not found` when trying to run the CLI, this means that the installation location of Python package executables (“binaries”) are not present on your system path. This is a common problem; you need to reconfigure your system’s environment variables to fix it.  
>* **Linux(WSL)**  
>```bash
>python3 -m site --user-base
>```
>
>We know that the scripts go to the `bin/` folder where the packages are installed. So we concatenate the paths with `bin/`. Finally, in order to avoid repeating the `export` command we add it to our `.bashrc` file and we run `source` to run the new changes.  
>```bash
>export PATH="/home/hhq/.local/bin:$PATH"
>```
>* **Windows PowerShell**  
>```bash
>python -m site --user-site
>```
>
>you can find the user base binary directory and replacing `site-packages` with `Scripts`. For example, this could return 			`C:\Users\<Username>\AppData\Roaming\Python\Python311\site-packages` so you would need to set your PATH to include `C:\Users\<Username>\AppData\Roaming\Python\Python311\Scripts`. You can set your user PATH permanently in the Control Panel. You may need to log out for the PATH changes to take effect.  

#### Upgrade
Upgrade the package with `pip`(macOS, Linux, WSL, Windows PowerShell):  
```bash
pip install --upgrade everai
```
#### Uninstall
Uninstall the package with `pip`(macOS, Linux, WSL, Windows PowerShell):  
```bash
pip uninstall everai
```

## Create an app
### File and folder structure
* **`Dockerfile`** This is the text file which is used to build docker image, and it includes series of instructions and commands used to build image.  

* **`app.py`** This is the code file which mainly supplies public service.  

* **`image_builder.py`** This is the config file that is used to build docker image.

```bash
<app name>
├── Dockerfile
├── app.py
└── image_builder.py
```

### Create your first app
```bash
everai login --token <your token>

everai app create get-start
```

## Create secrets
Secrets provide a dictionary of environment variables for images.  

Secrets are a secure way to add credentials and other sensitive information to the containers your functions run in.  

You can create and edit secrets on EverAI, or programmatically from Python code.  

Depending on whether the model and Docker image require security certification.  

In this case, we will create one secret for [quay.io](https://quay.io/) . 
```bash
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```

## Write your app code in python
There is an example code in `app.py`.  

You could test in your local machine will following command.  

```bash
everai app run
```

## Prepare volume
Before your application is deployed in the cloud, you should construct your volume first, if your app uses at least one volume.  

For production environment, the volumes are very important, you could call the following command to prepare it.  

This command line will call all functions which are decorated by `@app.prepare`, in these functions you should set up volume data before the app use it.  
```bash
everai app prepare
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
docker login quary.io

docker buildx version
```
Then call the following command will compile the image and push them to your specified registry.  
```bash
everai image build
```

## Deploy image
The final step is to deploy your app to [EverAI](everai.expvent.com) and keep it running.
```bash
everai app deploy  
```
After running `everai app list`, you can see your apps status. If status is `STATUS_DEPLOYED`, it means that your app is deployed successfully.   
```bash
NAME         STATUS                       CREATED_AT                ROUTE_NAME
-----------  ---------------------------  ------------------------  ------------
get-start    V1AppStatus.STATUS_DEPLOYED  2024-04-29 15:05:18+0800  got-started
```
Now, you can make a test call for your app, in these examples looks like:  
```bash
curl --no-buffer -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/apps/v1/routes/got-started/sse

curl -X POST -H'Content-Type: application/json' -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/apps/v1/routes/got-started/txt2img/jone -d '{"prompt": "say hello to"}'
```

## License
The MIT License.  

## Contributing
