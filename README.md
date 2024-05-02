# READ ME
## 简介Introduction
EverAI's platform empowers data/AI/ML teams to develop faster at lower cost, while scaling production workloads to thousands of CPUs and GPUs.  
Run generative AI models, large-scale batch jobs, job queues, and much more.  
Run GPU inference jobs on serverless infrastructure that scales with you.  
EverAI CLI is the tool to automate / manage GPU pods for EverAI.  
The EverAI Python library provides convenient, on-demand access to serverless cloud compute from Python scripts on your local computer.  

## 产品特性Features
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

## 安装和使用前提条件Requirements/Prerequisites
### Python
EverAI CLI library requires Python 3.8+.  
EverAI CLI需要Python版本为3.8及以上版本。
### EverAI
To continue with this quick start, you'll need access to the following from EverAI:  
* **EverAI account**  
Create an account at everai.expvent.com.  
你需要在everai.expvent.com创建一个账号。  
* **EverAI Token**  
	你可以从everai.expvent.com中获取到你的账号token。  
### Docker
To build your Docker image, you'll need access to the following:  
* **Docker installed**  
* **Docker Buildx installed**  
* **Docker account**  

## 快速入门Quick start
### 安装，更新和卸载Install, Upgrade and Uninstall
#### 安装Install
使用pip工具进行安装。  
Run this in order to install the Python library locally.  
Install the package with `pip`:  
##### Linux(WSL)  
```bash
pip install everai
```

##### MacOS
```bash  
pip install everai
```

##### Windows PowerShell  
以管理员身份运行PowerShell：  
```bash
pip install everai --user
```

注意Tips  
If you installed EverAI CLI but you’re seeing an error like everai: command not found when trying to run the CLI, this means that the installation location of Python package executables (“binaries”) are not present on your system path. This is a common problem; you need to reconfigure your system’s environment variables to fix it.  
* **Linux**  
```bash
python3 -m site --user-base

export PATH="/home/hhq/.local/bin:$PATH"
```

We know that the scripts go to the `bin/` folder where the packages are installed.So we concatenate the paths with `bin/`. Finally, in order to avoid repeating the export command we add it to our `.bashrc` file and we run source to run the new changes, giving us the suggested solution mentioned at the beginning.  
* **Windows PowerShell**  
```bash
pip install everai --user

 Installing collected packages: everai
  WARNING: The scripts ever.exe and everai.exe are installed in 'C:\Users\黄河清\AppData\Roaming\Python\Python311\Scripts' which is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
Successfully installed everai-0.1.38
```
或者运行：  
```bash
python -m site --user-site
```

you can find the user base binary directory and replacing `site-packages` with `Scripts`. For example, this could return 			`C:\Users\<Username>\AppData\Roaming\Python\Python311\site-packages` so you would need to set your PATH to include `C:\Users\<Username>\AppData\Roaming\Python\Python311\Scripts`. You can set your user PATH permanently in the Control Panel. You may need to log out for the PATH changes to take effect.
在系统属性的环境变量中，设置`C:\Users\<Username>\AppData\Roaming\Python\Python311\Scripts`。
#### 更新Upgrade
Upgrade the package with pip(macOS, Linux(WSL), Windows PowerShell):  
```bash
pip install --upgrade everai
```
#### 卸载Uninstall
Uninstall the package with pip(macOS, Linux(WSL), Windows PowerShell):  
```bash
pip uninstall everai
```

## 创建应用Create an app
### 文件目录结构File and folder structure
按照目录来区分app，要写入文档，每个app必须包含的文件介绍，包括app.py，image_builder.py等
```bash
hhq@HHQ:~/app$ tree hhq-start
hhq-start
├── Dockerfile
├── __pycache__
│   ├── app.cpython-310.pyc
│   └── image_builder.cpython-310.pyc
├── app.py
├── image_builder.py
└── requirements.txt
```

### 创建第一个应用Create your first app
```bash
everai login --token <your token>

everai app create get-start
```

## 创建密钥Create secrets
Secrets provide a dictionary of environment variables for images.
Secrets are a secure way to add credentials and other sensitive information to the containers your functions run in. You can create and edit secrets on EverAI, or programmatically from Python code.
depending on whether the model and Docker image require security certification  
In this case, we will create one secret for quay.io  
```bash
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```

## 编写你的代码Write your app code in python
There is an example code in `app.py`  
you could test in your local machine will following command  
```bash
everai app run
```

## 准备存储Prepare volume
Before your application cloud be deployed, you should construct your volume first, if your app use at least one volume.  
For production environment, the volumes are very important, you could call the following command to prepare it.  
This command line will call all functions who decorated by `@app.prepare`, in these functions you should set up volume data before the app use it  
```bash
everai app prepare
```

## 构建镜像Build image
This step will build the container image, using two very simple files `Dockerfile` and `image_builder.py`, and call the following command will compile the image and push them to your specified registry.  
The dependence of this step is docker and buildx installed on your machine. Otherwise we will have further prompts to help you install them  
```bash
docker login quary.io
docker buildx version
everai image build
```
image_builder.py  
```python
from everai.image import Builder

#Example: quay.io/riverhuang1107/get-start:v0.0.30
IMAGE = 'quay.io/<username>/<repo>:<tag>'
```
`everai image build`过程中会安装`buildx`，`buildx`用于构建多平台（x86，arm64）构架镜像  


## 部署Deploy image
The final step is to deploy your app to everai and keep it running.
```bash
everai app deploy  
```

Now, you can make a test call for your app, in this example looks like  
```bash
curl --no-buffer -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/apps/v1/routes/got-started/sse

curl -X POST -H'Content-Type: application/json' -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/apps/v1/routes/got-started/txt2img/jone -d '{"prompt": "say hello to"}'
```

## 开源协议License
The MIT License.  

## 参与贡献Contributing
