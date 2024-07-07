# 使用manifest yaml文件创建第一个应用
在[快速入门](https://github.com/everai-example/get-start)中，我们已经了解了如何通过在[app.py](https://github.com/everai-example/get-start/blob/main/app.py)中创建一个app对象来创建一个EverAI应用。除了使用app对象这种方法来创建应用，我们还提供了一种方法，通过定义一个manifest yaml文件，即可快速创建应用。这种方法无需在你的已有的业务代码中引入EverAI SDK代码，便可以把你的应用部署到EverAI平台。

## 创建应用目录
### 文件目录结构
在[EverAI](https://everai.expvent.com)平台创建应用前，首先要为应用创建一个文件夹，一般以你的应用名称命名。一个EverAI应用一般至少由以下四个文件构成：  
* **`Dockerfile`** 用来构建镜像的文本文件，文本内容包含了构建镜像所需的指令和操作命令。  

* **`app.py`** 对外提供应用服务的代码文件。  

* **`app.yaml`** manifest文件，文件定义了创建EverAI应用所需要的各种信息，包括应用名称，镜像名称，密钥信息，数据卷信息等。

* **`requirements.txt`** 文件定义了运行Python应用的依赖包以及对应软件版本。你可以使用`pipreqs .`获取到最新软件依赖包和对应版本。

```bash
<your app name>
├── Dockerfile
├── app.py
├── app.yaml
├── requirements.txt
├── volume -> /root/.cache/everai/volumes/NYygLnXsD9sHYtn9C7y7hN
└── volume-test -> /root/.cache/everai/volumes/hupa49MesPXwmhFSy9Ku44
```

### 登录EVERAI客户端
进入应用目录后，首先需要使用你从[EverAI](https://everai.expvent.com)获取到的token进行登录。  

```bash
everai login --token <your token>
```

## 编写你的代码

### 准备存储
如果你的应用需要用到文件对象存储，那么在你的应用部署到云环境之前，你需要至少先创建一个卷。  

在生产环境，文件对象存储是非常重要的，你可以通过下面的命令准备它。  

```bash
everai volume create get-start-volume
```

你可以通过`everai volume get`命令获取到卷`get-start-volume`的本地路径。进入卷的本地路径后，可以把你的文件存储在这个路径下。 在示例代码中，文件`my-model`被存放在该路径下。

```bash
everai volume get get-start-volume
<Volume: id: NYygLnXsD9sHYtn9C7y7hN, name: get-start-volume, revision: 000001-2bd, files: 1, size: 11 B>
path: /root/.cache/everai/volumes/NYygLnXsD9sHYtn9C7y7hN
```

在当前应用目录下，创建一个软连接来挂载卷`get-start-volume`的本地路径。  

```bash
ln -s /root/.cache/everai/volumes/NYygLnXsD9sHYtn9C7y7hN volume
```

在示例代码中，卷`get-start-volume`在本地环境中的文件`my-model`会通过`everai volume push`命令，推送到云端。

```bash
everai volume push get-start-volume
```

### 实现API端点服务

这是一个关于[app.py](https://github.com/everai-example/get-start-manifest/blob/main/app.py)的示例代码。  

现在你可以编写自己的代码，实现API端点服务。这里的示例使用`flask`实现了一个读取卷`get-start-volume`中文件`my-model`信息的对外服务。

```python
import os

from flask import Flask

app = Flask(__name__)

VOLUME_NAME = 'volume'
VOLUME_TEST_NAME = 'volume-test'
MODEL_FILE_NAME = 'my-model'

# curl http://localhost:8866/show-volume
@app.route('/show-volume', methods=['GET'])
def show_volume():
    model_path = os.path.join(VOLUME_NAME, MODEL_FILE_NAME)
    with open(model_path, 'r') as f:
        return f.read()

# curl http://localhost:8866/show-volume-test
@app.route('/show-volume-test', methods=['GET'])
def show_volume_test():
    model_path = os.path.join(VOLUME_TEST_NAME, MODEL_FILE_NAME)
    with open(model_path, 'r') as f:
        return f.read()
        
if __name__ == '__main__':
    app.run(host="0.0.0.0", debug=False, port=8866)
```

现在，你可以通过下面的命令在你的本地环境测试你的代码。  

```bash
python app.py
```

使用`curl`来请求这个API端点服务，并看到在你的终端上显示`hello world`。    

```bash
curl http://<your ip>:8866/show-volume
```

此外，在同一个应用中，你可以实现多个API端点服务。这里的示例使用`flask`又实现了一个服务器端向客户端推送消息的对外服务。

```python
import time
import flask

# https://everai.expvent.com/api/routes/v1/default/get-start-manifest/sse
# http://localhost:8866/sse
@app.route('/sse', methods=['GET'])
def sse():
    def generator():
        for i in range(10):
            yield f"hello again {i}"
            time.sleep(1)

    return flask.Response(generator(), mimetype='text/event-stream')
```

再次执行`python app.py`，使用`curl`来请求这个`SSE`（Server-Sent Events）服务，并看到10秒内，有10个SSE事件出现在你的终端上。

```bash
curl --no-buffer http://<your ip>:8866/sse
```

## 构建镜像
这步需要使用`Dockerfile`来为你的应用构建容器镜像。  

这是一个[Dockerfile](https://github.com/everai-example/get-start-manifest/blob/main/Dockerfile)的示例代码。

```
WORKDIR /workspace

RUN mkdir -p $WORKDIR/volume
RUN mkdir -p $WORKDIR/volume-test
```

在这个示例中，我们选择使用[quay.io](https://quay.io/)作为公共镜像仓库，存放应用镜像。你也可以使用与之类似的知名镜像仓库，如：[Docker Hub](https://hub.docker.com/)，[GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)，[Google Container Registry](https://cloud.google.com/artifact-registry)等。如果你有自建的镜像仓库，并且镜像可以在互联网上被访问，同样可以使用。  

首先确保你的docker环境处于登录状态。  

```bash
docker login quay.io

docker build -t quay.io/<username>/<repo>:<tag> .
```
然后执行以下命令打包镜像，并且把打包好的镜像推送到你指定的镜像仓库中。
```bash
docker push quay.io/<username>/<repo>:<tag>
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

## 创建configmap
>该步骤可选，如果你配置了configmap，你可以在部署镜像后使用configmap调整你的自动扩缩容策略。 
```shell
everai configmap create get-start-configmap \ 
  --from-literal min_workers=1 \
  --from-literal max_workers=5 \
  --from-literal max_queue_size=2 \
  --from-literal scale_up_step=1 \
  --from-literal max_idle_time=60
```

## 定义Manifest文件

manifest文件定义了创建EverAI应用所需要的各种信息，包括应用名称，镜像名称，密钥信息，数据卷信息等。  

这是一个关于[app.yaml](https://github.com/everai-example/get-start-manifest/blob/main/app.yaml)的示例代码。

```yaml
version: everai/v1alpha1
kind: App
metadata:
  name: <your app name>                          # application name
spec:
  image: quay.io/<username>/<repo>:<tag>       # image for serverless app
  imagePullSecrets:
    username:
      valueFrom:
        secretKeyRef:
          name: quay-secret
          key: username
    password:
      valueFrom:
        secretKeyRef:
          name: quay-secret
          key: password
  volumeMounts:
    - name: get-start-volume                                    # name
      mountPath: /workspace/volume       # mount path in container
      readOnly: true                              # only support `readOnly = true` currently, default is true
    - name: test-start-volume                                    # name
      mountPath: /workspace/volume-test       # mount path in container
      readOnly: true                              # only support `readOnly = true` currently, default is true

  port: 8866                                        # just one port cloud be set, everai will pass any http request /**
                                                  # to this port, default is 80
  readinessProbe:                                 # if readinessProbe is set up, there are no any request be route
                                                  # to this worker before probe status is ready ( status code = 200 ),
                                                  # otherwise (readinessProbe is not set up), everai will route reqeust
                                                  # to this worker when container is ready,
                                                  # even model not loaded into memory of gpu
    httpGet:                                      # http get and post probe is the only supported methods now
      path: /healthy-check                     # only http status 200 means ready

  volumes:                                        # optional field, but very important for AI app
    - name: get-start-volume                                    # volume name
      volume: 
        volume: get-start-volume          # use a private volume or a public volume from other user
    - name: test-start-volume                                    # volume name
      volume:
        volume: test-start-volume          # use a private volume or a public volume from other user
    - name: quay-secret
      secret:
        secretName: quay-secret

  resource:
    cpu: 1
    memory: 1 GiB
```

## 部署
最后一步是把你的应用部署到[EverAI](https://everai.expvent.com)。并使它保持在可用的状态。  
```bash  
everai app create --from-file app.yaml
```
执行`everai app list`后，可以看到类似如下的输出结果。如果你的应用状态是`DEPLOYED`，意味着你的应用已经部署成功。    
```bash
NAME                             NAMESPACE    STATUS    WORKERS    CREATED_AT
-------------------------------  -----------  --------  ---------  ------------------------
get-start-manifest               default      DEPLOYED  1/1        2024-06-29T08:32:33+0000
```
当你看到你的应用处于`DEPLOYED`时，你可以执行下面的请求来测试你部署的代码是否符合你的预期：  
```bash
curl --no-buffer -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/sse
```