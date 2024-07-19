# 应用热升级
## update volume

如果你的应用已经在[EverAI](https://everai.expvent.com)平台云端部署，当你需要更新卷中的文件内容时，可以使用`everai app update`。[EverAI](https://everai.expvent.com)平台支持应用热升级，你的应用服务在整个升级更新过程中，始终处于在线运行状态。  

以下示例基于[快速入门](https://github.com/everai-example/get-start)中的应用。  

运行`everai worker list`，看到有一个worker正在运行中。`CREATED_AT`和`DELETED_AT`使用UTC时间显示。  
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
kEEBkaoEaZrxPgzab2ChjQ  RUNNING   FREE             2024-07-05T08:41:49+0000
```

使用`curl`执行测试用例，在终端显示如下的数据信息。  
```bash
curl -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/show-volume
hello world
hello world
```

执行`everai volume get get-start-volume`，可以看到你的应用中使用到的卷的存放路径。  
```bash
<Volume: id: R9cFpk2JgmLcuiEfgAxPRd, name: get-start-volume, revision: 000001-08a, files: 1, size: 11 B>
path: /Users/<username>/.cache/everai/volumes/R9cFpk2JgmLcuiEfgAxPRd
```

进入卷的所在路径，找到应用中使用到的文件`my-model`，打开文件修改文件内容后保存退出。在示例中，把文件内容更新为如下数据。  
```bash
hello world
hello world
hello world
hello world
```

回到你应用的所在目录，执行`everai app prepare`，把更新后的文件推送到云端。  

执行如下命令，完成应用的升级操作。  
```bash
everai app update
```

再次运行`everai worker list -a`，看到原来的worker依然在运行中。同时一个全新的worker已经在部署中。 
```bash
ID                      STATUS      DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  ----------  ---------------  ------------------------  ------------------------
kEEBkaoEaZrxPgzab2ChjQ  RUNNING     FREE             2024-07-05T08:41:49+0000
mNTAJoqyMRHDyDoLTCSjPn  CREATED     IN_FLIGHT        2024-07-05T13:11:59+0000
```

直到系统把新的worker部署完成后，原来的worker会被停止工作。执行`everai worker list -a`可以看到新的worker已经处于运行状态。  
```bash
ID                      STATUS      DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  ----------  ---------------  ------------------------  ------------------------
kEEBkaoEaZrxPgzab2ChjQ  TERMINATED                   2024-07-05T08:41:49+0000  2024-07-05T13:12:23+0000
mNTAJoqyMRHDyDoLTCSjPn  RUNNING     FREE             2024-07-05T13:11:59+0000
```

再次使用`curl`执行测试用例，在控制台显示如下的数据信息。可以看到显示的数据信息已经完成更新。  
```bash
curl -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/show-volume
hello world
hello world
hello world
hello world
```

## update image  

如果你的应用已经在[EverAI](https://everai.expvent.com)平台云端部署，当你需要更新你应用中的代码时，可以使用`everai app update`。[EverAI](https://everai.expvent.com)平台支持应用热升级，你的应用服务在整个升级更新过程中，始终处于在线运行状态。  

以下示例基于[快速入门](https://github.com/everai-example/get-start)中的应用。  

运行`everai worker list`，看到有一个worker正在运行中。`CREATED_AT`和`DELETED_AT`使用UTC时间显示。  
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
PNLENbRP7AabNy6ZQWTogb  RUNNING   FREE             2024-07-05T06:45:19+0000
```

使用`curl`执行测试用例，在终端显示如下的数据信息。服务器端向客户端持续推送消息。  
```bash
curl -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/sse
hello again 0

hello again 1

hello again 2
```

这一步，打开`app.py`文件，把`hello again`改为`hello world again`。你执行`everai app run`确保你的代码运行正常。  
```python
import flask

@app.service.route('/sse', methods=['GET'])
def sse():
    def generator():
        for i in range(10):
            yield f"hello world again {i}\n\n"
            time.sleep(1)

    return flask.Response(generator(), mimetype='text/event-stream')
```

打开`image_builder.py`文件，更新需要上传镜像的版本号。并执行`everai image build`进行镜像构建，并推送镜像到[quay.io](https://quay.io/)镜像仓库。  

```python
from everai.image import Builder

IMAGE = 'quay.io/<username>/<repo>:<tag>'
```

镜像构建完成后，你需要执行如下命令进行应用更新。  
```bash
everai app update
```

运行`everai worker list -a`，可以看到新的worker正处于`INITIALIZED`状态。  
```bash
ID                      STATUS       DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  -----------  ---------------  ------------------------  ------------
PNLENbRP7AabNy6ZQWTogb  RUNNING      FREE             2024-07-05T06:45:19+0000
kEEBkaoEaZrxPgzab2ChjQ  INITIALIZED  IN_FLIGHT        2024-07-05T08:41:49+0000
```
直到新的worker处于`RUNNING`状态，原来的worker已经处于`TERMINATED`状态。
```bash
ID                      STATUS      DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  ----------  ---------------  ------------------------  ------------------------
PNLENbRP7AabNy6ZQWTogb  TERMINATED                   2024-07-05T06:45:19+0000  2024-07-05T08:42:28+0000
kEEBkaoEaZrxPgzab2ChjQ  RUNNING     FREE             2024-07-05T08:41:49+0000
```

再次使用`curl`执行测试用例，在终端控制台显示如下的数据信息。可以看到服务器端向客户端持续推送消息已经被更新。  
```bash
curl -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/sse
hello world again 0


hello world again 1


hello world again 2


hello world again 3
```

## update resource

如果你的应用已经在[EverAI](https://everai.expvent.com)平台云端部署，当你需要调整你的计算资源（CPU，内存，GPU卡数量或者GPU型号）时，可以使用`everai app update`。[EverAI](https://everai.expvent.com)平台支持应用热升级，你的应用服务在整个升级更新过程中，始终处于在线运行状态。  

打开`app.py`文件，在通过App类来创建定义一个app实例的代码中，修改`resource_requests`参数：  
```python
resource_requests=ResourceRequests(
    cpu_num=2,
    memory_mb=20480,
    gpu_num=1,
    gpu_constraints=[
        "A100 40G",
        "RTX 4090"
    ],
)
```

保存退出`app.py`文件后，执行如下命令，完成应用计算资源的调整。  

```bash
everai app update
```

运行`everai worker list -a`，看到原来在运行的worker正处于`TERMINATED`状态，新的worker已经在运行中。