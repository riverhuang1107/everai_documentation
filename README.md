# README
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
在快速入门前，你首先需要在[EverAI](everai.expvent.com)完成以下准备工作：
* **EverAI账号**  

  你需要在[EverAI](everai.expvent.com)创建一个账号。  

* **EverAI Token**  

	你可以从[EverAI](everai.expvent.com)中获取到你的账号token。  

### Docker
为了能顺利构建Docker镜像，你需要做以下准备工作：  
* **安装Docker环境**  

* **安装Docker Buildx插件**  

* **具备一个Docker账号**  

>**小贴士**  
>Buildx可用于构建多平台（x86，arm64）构架Docker镜像。


## 快速入门
### 安装，更新和卸载
#### 安装
使用`pip`工具安装EverAI CLI命令行工具。安装后，你可以在本地环境运行该工具。  
##### Linux(WSL) & MacOS  
```bash
pip install everai
```

##### Windows PowerShell  
以管理员身份运行PowerShell：  
```bash
pip install everai --user
```

>**小贴士**  
>如果你安装了EverAI CLI工具，但你在执行命令时发现了类似`everai: command not found`这样的错误，这意味着你的系统路径中还没有设置可以执行Python可执行包（二进制可执行包）的系统路径。这是一个常见问题；你需要通过配置你的系统环境变量来修复它。  
>* **Linux(WSL)**  
>```bash
>python3 -m site --user-base
>```
>
>执行上述命令获得目录，如`/home/<username>/.local/`，Python的二进制包文件安装在该目录的`bin/`下。我们可以把两者进行组合，得到路径`/home/<username>/.local/bin`。为了避免每次使用时设置该环境变量，我们可以把该命令加入到`.bashrc`，并且执行`source`命令，使之立即生效。  
>```bash
>export PATH="/home/hhq/.local/bin:$PATH"
>```
>* **Windows PowerShell**  
>```bash
>python -m site --user-site
>```
>
>执行上述命令，你可以得到存放Python二进制包的基础路径，然后把路径中的`site-packages`替换成`Scripts`。举例来说，如果命令返回的是`C:\Users\<Username>\AppData\Roaming\Python\Python311\site-packages`，那么你需要在你的环境变量中添加`C:\Users\<Username>\AppData\Roaming\Python\Python311\Scripts`。你可以在系统属性的环境变量中，添加这条路径。最后你需要关闭`PowerShell`窗口，重新打开后使之生效。  

#### 更新
使用`pip`工具升级EverAI CLI命令行工具，以下命令适用于Linux(WSL)，macOS， Windows PowerShell。  
```bash
pip install --upgrade everai
```
#### 卸载
使用`pip`工具卸载EverAI CLI命令行工具，以下命令适用于Linux(WSL)，macOS， Windows PowerShell。  
```bash
pip uninstall everai
```

## 创建应用
### 文件目录结构
在[EverAI](everai.expvent.com)平台创建应用前，首先要为应用创建一个文件夹，一般以你的应用名称命名。一个EverAI应用必须至少由以下三个文件构成：  
* **`Dockerfile`** 用来构建镜像的文本文件，文本内容包含了构建镜像所需的指令和操作命令。  

* **`app.py`** 对外提供应用服务的代码文件。  

* **`image_builder.py`** 构建镜像的配置文件。  

```bash
<app name>
├── Dockerfile
├── app.py
└── image_builder.py
```

### 创建第一个应用
```bash
everai login --token <your token>

everai app create get-start
```

## 创建密钥
密钥管理提供了一种安全的方法，可以添加凭证和其他敏感信息到你的应用容器中。  

是否需要创建密钥，取决于下载模型文件和拉取Docker镜像时是否需要安全验证。  

你可以在[EverAI](everai.expvent.com)中创建和编辑密钥，或者通过编写Python代码来管理它。  

在这个例子中，我们会为[quay.io](https://quay.io/)创建一个密钥。  

```bash
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```

## 编写你的代码
这是一个关于`app.py`的示例代码。  

你可以通过下面的命令在你的本地环境测试你的代码。  

```bash
everai app run
```

## 准备存储
如果你的应用需要用到文件对象存储，那么在你的应用部署到云环境之前，你需要先创建一个对象存储。  

在生产环境，对象存储是非常重要的，你可以通过下面的命令准备它。  

这句命令会执行所有被`@app.prepare`注解过的方法，在这些方法中你应该配置好文件数据。  

```bash
everai app prepare
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
docker login quary.io

docker buildx version
```
然后执行以下命令打包镜像，并且把打包好的镜像推送到你指定的镜像仓库中。
```bash
everai image build
```


## 部署
最后一步是把你的应用部署到[EverAI](everai.expvent.com)。并使它保持在可用的状态。  
```bash
everai app deploy  
```
执行`everai app list`后，可以看到当前应用的状态。如果应用状态是`STATUS_DEPLOYED`，意味着你的应用已经部署成功。    
```bash
NAME         STATUS                       CREATED_AT                ROUTE_NAME
-----------  ---------------------------  ------------------------  ------------
get-start    V1AppStatus.STATUS_DEPLOYED  2024-04-29 15:05:18+0800  got-started
```
当你看到你的应用处于`STATUS_DEPLOYED`时，你可以执行下面的请求来测试你部署的代码是否符合你的预期：  
```bash
curl --no-buffer -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/apps/v1/routes/got-started/sse

curl -X POST -H'Content-Type: application/json' -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/apps/v1/routes/got-started/txt2img/jone -d '{"prompt": "say hello to"}'
```

## 开源协议
MIT License协议。  

## 参与贡献
