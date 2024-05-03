# READ ME
## 简介
EverAI平台提供数据/AI/机器学习研发团队以更低的运营研发成本来快速迭代产品，尤其是在你团队的AI产品运行时需要消耗大规模CPU和GPU计算资源的时候。  
EverAI平台提供无服务云计算架构的运行环境，可运行生成式AI大模型、大规模批处理任务，以及任务队列等。  
EverAI CLI命令行工具提供了一种按需计费和使用的方法，使你的本地Python代码以无服务的云计算方式在EverAI平台上运行。  

## 产品特性
* **按需付费**  
你只需要为你真正在使用的计算资源时长支付费用。你不再需要为空闲时的资源支付任何费用。并且是按小时计费。  
* **关注你的代码**  
以无服务的云计算架构方式部署你的应用。你不用关注硬件基础设施。我们来负责硬件底层的基础设施。  
* **提供多种型号的GPU**  
你可以根据你的应用场景选择合适的GPU型号。当你的应用遇到性能瓶颈时，你的AI推理应用可以在你配置的GPU型号上自动扩容。  
* **本地开发远端运行**  
你可以在你的笔记本上开发，并且在云环境下轻便快速的构建，立即执行代码。在云环境中高效和可扩展地运行你的AI模型软件。  
* **在云端储存大量文件**  
你可以在高效的分布式文件系统中读写数据。


## 安装和使用前提条件
### Python
EverAI CLI需要Python版本为3.8及以上版本。

### EverAI
在快速入门前，你首先需要在[EverAI](everai.expvent.com)完成完成以下的准备工作：
* **EverAI账号**  
你需要在[EverAI](everai.expvent.com)创建一个账号。
* **EverAI Token**  
	你可以从[EverAI](everai.expvent.com)中获取到你的账号token。
### Docker
为了能顺利构建Docker镜像，你需要做以下准备工作：  
* **安装Docker环境**
* **安装Docker Buildx插件**
* **具备一个Docker账号**
>小贴士：  
>Buildx可用于构建多平台（x86，arm64）构架Docker镜像。


## 快速入门
### 安装，更新和卸载
#### 安装
使用pip工具进行安装。  
Run this in order to install the Python library locally.  
Install the package with `pip`:  
##### Linux(WSL) & MacOS  
```bash
pip install everai
```

##### Windows PowerShell  
以管理员身份运行PowerShell：  
```bash
pip install everai --user
```

>**小贴上**  
>If you installed EverAI CLI but you’re seeing an error like `everai: command not found` when trying to run the CLI, this means that the installation location of Python package executables (“binaries”) are not present on your system path. This is a common problem; you need to reconfigure your system’s environment variables to fix it.  
>如果你安装了EverAI CLI工具，但你在执行命令时发现了类似`everai: command not found`这样的错误，这意味着你的系统路径中还没有设置可以执行Python可执行包（二进制可执行包）的系统路径。这是一个常见问题；你需要通过配置你的系统环境变量来修复它。  
>* **Linux**  
>```bash
>python3 -m site --user-base
>
>export PATH="/home/hhq/.local/bin:$PATH"
>```
>
>We know that the scripts go to the `bin/` folder where the packages are installed. So we concatenate the paths with `bin/`. Finally, in order to avoid repeating the export command we add it to our `.bashrc` file and we run `source` to run the new changes, giving us the suggested solution mentioned at the beginning.  
>* **Windows PowerShell**  
>```bash
>pip install everai --user
>
> Installing collected packages: everai
>  WARNING: The scripts ever.exe and everai.exe are installed in 'C:\Users\黄河清\AppData\Roaming\Python\Python311\Scripts' which is not on PATH.
>  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
>Successfully installed everai-0.1.38
>```
>或者运行：  
>```bash
>python -m site --user-site
>```
>
>you can find the user base binary directory and replacing `site-packages` with `Scripts`. For example, this could return 			`C:\Users\<Username>\AppData\Roaming\Python\Python311\site-packages` so you would need to set your PATH to include `C:\Users\<Username>\AppData\Roaming\Python\Python311\Scripts`. You can set your user PATH permanently in the Control Panel. You may need to log out for the PATH changes to take effect.  
>在系统属性的环境变量中，设置`C:\Users\<Username>\AppData\Roaming\Python\Python311\Scripts`。
#### 更新
Upgrade the package with pip(macOS, Linux(WSL), Windows PowerShell):  
```bash
pip install --upgrade everai
```
#### 卸载
Uninstall the package with pip(macOS, Linux(WSL), Windows PowerShell):  
```bash
pip uninstall everai
```

## 创建应用
### 文件目录结构
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

### 创建第一个应用
```bash
everai login --token <your token>

everai app create get-start
```

## 创建密钥
Secrets provide a dictionary of environment variables for images.
Secrets are a secure way to add credentials and other sensitive information to the containers your functions run in. You can create and edit secrets on EverAI, or programmatically from Python code.
depending on whether the model and Docker image require security certification  
In this case, we will create one secret for [quay.io](https://quay.io/)  
```bash
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```

## 编写你的代码
There is an example code in `app.py`  
you could test in your local machine will following command  
```bash
everai app run
```

## 准备存储
Before your application cloud be deployed, you should construct your volume first, if your app use at least one volume.  
For production environment, the volumes are very important, you could call the following command to prepare it.  
This command line will call all functions who decorated by `@app.prepare`, in these functions you should set up volume data before the app use it  
```bash
everai app prepare
```

## 构建镜像
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


## 部署
The final step is to deploy your app to everai and keep it running.
```bash
everai app deploy  
```

Now, you can make a test call for your app, in this example looks like  
```bash
curl --no-buffer -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/apps/v1/routes/got-started/sse

curl -X POST -H'Content-Type: application/json' -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/apps/v1/routes/got-started/txt2img/jone -d '{"prompt": "say hello to"}'
```

## 开源协议
The MIT License.  

## 参与贡献
