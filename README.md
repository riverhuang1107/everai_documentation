---
sidebar_position: 3
---

# 新手指引
## 简介
EverAI平台提供数据/AI/机器学习研发团队以更低的运营研发成本来快速迭代产品，尤其是在你团队的AI产品运行时需要消耗大规模CPU和GPU计算资源的时候。  

EverAI平台提供无服务云计算架构的运行环境，可运行生成式AI大模型、大规模批处理任务，以及支持任务队列等。  

EverAI CLI是EverAI平台的命令行工具，它通过命令行交互方式管理你在EverAI平台上的应用，密钥，存储文件等资源。  

EverAI CLI命令行工具提供了一种便捷的按需计费的接入方式，可以使你的本地Python代码以无服务的云计算形式在EverAI平台上运行。  

## 产品特性
* **按需付费**  
你只需要为你真正在使用的计算资源时长支付费用。你不再需要为空闲时的资源支付任何费用。并且按秒计费。  

* **关注你的代码**  
以无服务的云计算架构方式部署你的应用。你不用关注硬件基础设施。我们来负责底层硬件的基础设施。  

* **提供多种型号的GPU**  
你可以根据你的应用场景选择合适的GPU型号。当你的应用遇到性能瓶颈时，你的AI推理应用可以在你配置的GPU型号上自动扩容。  

* **本地开发远端运行**  
你可以在你的笔记本上开发，并且在云环境下轻便快速的构建，立即执行代码。在云环境中高效和可扩展地运行你的AI模型软件。  

* **在云端储存大量文件**  
你可以在高效的分布式文件系统中读写数据。


## 安装和使用前提条件
### Python
EverAI CLI需要Python版本为3.10及以上版本。

### EverAI
在快速入门前，你首先需要在[EverAI](https://everai.expvent.com)完成以下准备工作：
* **EverAI账号**  

  你需要在[EverAI](https://everai.expvent.com)创建一个账号。  

* **EverAI Token**  

	你可以从[EverAI](https://everai.expvent.com)中获取到你的账号token。  

### Docker
为了能顺利构建Docker镜像，你需要做以下准备工作：  
* **安装Docker环境**  

* **安装Docker Buildx插件**  

* **具备一个Docker账号**  

>**小贴士**  
>
>Buildx用于构建多平台（x86，arm64）构架Docker镜像。

## 安装，更新和卸载
### 安装
使用`pip`工具安装EverAI CLI命令行工具。安装后，你可以在本地环境运行该工具。  
#### Linux(WSL) & MacOS  
```bash
pip install everai
```

#### Windows PowerShell  
以管理员身份运行PowerShell：  
```bash
pip install everai --user
```

>**小贴士**  
>
>如果你安装了EverAI CLI工具，但你在执行命令时发现了类似`everai: command not found`这样的错误，这意味着你的系统路径中还没有设置可以执行Python可执行包（二进制可执行包）的系统路径。这是一个常见问题；你需要通过配置你的系统环境变量来修复它。  
>* **Linux(WSL)**  
>```bash
>python3 -m site --user-base
>```
>
>执行上述命令获得目录，如`/home/<username>/.local/`，Python的二进制包文件安装在该目录的`bin/`下。我们可以把两者进行组合，得到路径`/home/<username>/.local/bin`。为了避免每次使用时设置该环境变量，我们可以把该命令加入到`.bashrc`，并且执行`source`命令，使之立即生效。  
>```bash
>export PATH="/home/<username>/.local/bin:$PATH"
>```
>* **Windows PowerShell**  
>```bash
>python -m site --user-site
>```
>
>执行上述命令，你可以得到存放Python二进制包的基础路径，然后把路径中的`site-packages`替换成`Scripts`。举例来说，如果命令返回的是`C:\Users\<Username>\AppData\Roaming\Python\Python311\site-packages`，那么你需要在你的环境变量中添加`C:\Users\<Username>\AppData\Roaming\Python\Python311\Scripts`。你可以在系统属性的环境变量中，添加这条路径。最后你需要关闭`PowerShell`窗口，重新打开后使之生效。  

### 更新
使用`pip`工具升级EverAI CLI命令行工具，以下命令适用于Linux(WSL)，macOS， Windows PowerShell。  
```bash
pip install --upgrade everai
```
### 卸载
使用`pip`工具卸载EverAI CLI命令行工具，以下命令适用于Linux(WSL)，macOS， Windows PowerShell。  
```bash
pip uninstall everai
```

## 创建应用目录
### 文件目录结构
在[EverAI](https://everai.expvent.com)平台创建应用前，首先要为应用创建一个文件夹，一般以你的应用名称命名。一个EverAI应用必须至少由以下四个文件构成：  
* **`Dockerfile`** 用来构建镜像的文本文件，文本内容包含了构建镜像所需的指令和操作命令。  

* **`app.py`** 对外提供应用服务的代码文件。  

* **`image_builder.py`** 构建镜像的配置文件。  

* **`requirements.txt`** 文件定义了运行Python应用的依赖包以及对应软件版本。你可以使用`pipreqs .`获取到最新软件依赖包和对应版本。

```bash
<your app name>
├── Dockerfile
├── app.py
├── image_builder.py
└── requirements.txt
```

### 登录EVERAI客户端
进入应用目录后，首先需要使用你从[EverAI](https://everai.expvent.com)获取到的token进行登录。  

```bash
everai login --token <your token>
```

## 创建密钥
密钥管理提供了一种安全的方法，可以添加凭证和其他敏感信息到你的应用容器中。  

是否需要创建密钥，取决于下载模型文件和拉取Docker镜像时是否需要安全验证。  

你可以在[EverAI](https://everai.expvent.com)中创建和编辑密钥，或者通过编写Python代码来管理它。  

在这个例子中，我们会为[quay.io](https://quay.io/)创建一个密钥。  

```bash
everai secret create quay-secret \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```
>**小贴士**  
>
>[quay.io](https://quay.io/)是一个知名的公共镜像仓库，与之类似的知名镜像仓库还有[Docker Hub](https://hub.docker.com/)，[GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)，[Google Container Registry](https://cloud.google.com/artifact-registry)等。

## 编写你的代码
### 基本设置
这是一个[app.py](https://github.com/everai-example/get-start/blob/main/app.py)的示例代码。  

首先，引入必要的EverAI Python类库。然后定义所需要用到的变量名，包括对象存储，访问镜像仓库的密钥，以及存放在对象存储中的文件等。使用`Image.from_registry`静态方法创建一个镜像实例。通过App类来创建定义一个app实例。  
```python
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

### 准备存储
如果你的应用需要用到文件对象存储，那么在你的应用部署到云环境之前，你需要至少先创建一个卷。  

你可以通过注解`@app.prepare`创建一个方法来管理和准备你的卷以及相关的文件。  

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

在生产环境，文件对象存储是非常重要的，你可以通过下面的命令准备它。  

```bash
everai app prepare
```

这句命令会执行所有被`@app.prepare`注解过的方法，在这些方法中你应该配置好文件数据。 

在示例代码中，卷`get-start-volume`在本地环境中的文件`my-model`会通过`everai app prepare`命令，推送到云端。

### 实现API端点服务

现在你可以编写自己的代码，实现API端点服务。这里的示例使用`flask`实现了一个读取卷`get-start-volume`中文件`my-model`信息的对外服务。

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

现在，你可以通过下面的命令在你的本地环境测试你的代码。  

```bash
everai app run
```

使用`curl`来请求这个API端点服务，并看到在你的终端上显示`hello world`。该信息是在执行上一步操作`prepare_model`时，写入文件`my-model`中的。  

```bash
curl http://<your ip>/show-volume
```

此外，在同一个应用中，你可以实现多个API端点服务。这里的示例使用`flask`又实现了一个服务器端向客户端推送消息的对外服务。

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

再次执行`everai app run`，使用`curl`来请求这个`SSE`（Server-Sent Events）服务，并看到10秒内，有10个SSE事件出现在你的终端上。

```bash
curl --no-buffer http://<your ip>/sse
```

## 构建镜像
这步需要使用`Dockerfile`和`image_builder.py`来为你的应用构建容器镜像。  

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
最后一步是把你的应用部署到[EverAI](https://everai.expvent.com)。并使它保持在可用的状态。  
```bash  
everai app create
```
执行`everai app list`后，可以看到类似如下的输出结果。如果你的应用状态是`DEPLOYED`，意味着你的应用已经部署成功。`CREATED_AT`使用UTC时间显示。    
```bash
NAME                             NAMESPACE    STATUS    WORKERS    CREATED_AT
-------------------------------  -----------  --------  ---------  ------------------------
get-start                        default      DEPLOYED  1/1        2024-07-10T05:38:24+0000
```
当你看到你的应用处于`DEPLOYED`时，你可以执行下面的请求来测试你部署的代码是否符合你的预期：  
```bash
curl --no-buffer -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/sse
```
## One more thing

到目前为止，你已经在[EverAI](https://everai.expvent.com)平台上成功部署了一个简单应用了。但这个应用的负载超出计算资源的可承受范围时，应用的服务响应会变慢，超时，甚至不可用。[EverAI](https://everai.expvent.com)平台提供了自动扩容机制，可以帮助你的应用在高负载的情况下自动扩容，省去你手动部署新计算节点的操作。关于这方面的介绍请参考[自动扩缩容](https://expvent.com/documentation/zh-cn/docs/Guide/App_Autoscaling)。 

## 开源协议
MIT License协议。  

## 参与贡献
