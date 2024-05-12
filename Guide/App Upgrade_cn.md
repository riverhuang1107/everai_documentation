# App Upgrade
## everai app upgrade --volume-requests

如果你的应用已经在[EverAI](https://everai.expvent.com)平台云端部署，当你需要更新你存储对象中的文件内容时，可以使用`everai app upgrade --volume-requests`。[EverAI](https://everai.expvent.com)平台支持应用热升级，你的应用服务在整个升级更新过程中，始终处于在线运行状态。  

运行`everai worker list`，看到有一个worker正在运行中。  
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
bqZJ4eTjMmEbP9ncrvPgGg  RUNNING   FREE             2024-05-11 19:24:08+0800
```

使用`curl`执行测试用例，在终端显示如下的数据信息。  
```bash
curl -H'Authorization: Bearer everai_637wE9obZtmGLyqIJp0lok' https://everai.expvent.com/api/apps/v1/routes/test-start-4/show-volume
hello world
hello world
```

执行`everai volume get get-start-volume`，可以看到你的应用中使用到的存储对象的存放路径。  
```bash
<Volume: id: R9cFpk2JgmLcuiEfgAxPRd, name: get-start-volume, revision: 000001-08a, files: 1, size: 11 B>
path: /Users/<username>/.cache/everai/volumes/R9cFpk2JgmLcuiEfgAxPRd
```

进入存储对象的所在路径，找到应用中使用到的文件`my-model`，打开文件修改文件内容后保存退出。在示例中，把文件内容更新为如下数据。  
```bash
hello world
hello world
hello world
hello world
```

回到你应用的所在目录，执行`everai app prepare`，把更新后的文件推送到云端。  

执行如下命令，完成应用的升级操作。  
```bash
everai app upgrade --volume-requests
```

再次运行`everai worker list`，看到原来的worker依然在运行中。同时一个全新的worker已经在部署中。  
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
bqZJ4eTjMmEbP9ncrvPgGg  RUNNING   FREE             2024-05-11 19:24:08+0800
GtxtbdHn2rFEkZqZxSesyE  RUNNING   NSPECIFIED       2024-05-12 18:20:38+0800
```

直到系统把新的worker部署完成后，原来的worker会被停止工作。执行`everai worker list`可以看到新的worker已经处于运行状态。  
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
GtxtbdHn2rFEkZqZxSesyE  RUNNING   FREE             2024-05-12 18:20:38+0800
```

再次使用`curl`执行测试用例，在控制台显示如下的数据信息。可以看到显示的数据信息已经完成更新。  
```bash
curl -H'Authorization: Bearer everai_637wE9obZtmGLyqIJp0lok' https://everai.expvent.com/api/apps/v1/routes/test-start-4/show-volume
hello world
hello world
hello world
hello world
```

## everai app upgrade --image  

如果你的应用已经在[EverAI](https://everai.expvent.com)平台云端部署，当你需要更新你存储对象中的文件内容时，可以使用`everai app upgrade --image`。[EverAI](https://everai.expvent.com)平台支持应用热升级，你的应用服务在整个升级更新过程中，始终处于在线运行状态。  

运行`everai worker list`，看到有一个worker正在运行中。  
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
dEXndYfRrpqwAirhBdugYN  RUNNING   FREE             2024-05-11 14:54:27+0800
```

使用`curl`执行测试用例，在终端显示如下的数据信息。服务器端向客户端持续推送消息。  
```bash
curl -H'Authorization: Bearer everai_637wE9obZtmGLyqIJp0lok' https://everai.expvent.com/api/apps/v1/routes/test-start-5/sse
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

```bash
from everai.image import Builder

IMAGE = 'quay.io/<username>/<repo>:<tag>'
```

镜像构建完成后，你需要执行如下命令进行应用更新。  
```bash
everai app upgrade --image
```
执行`everai app list`，可以看到需要被更新的应用的状态是`PREPARING`。  
```bash
NAME          STATUS     CREATED_AT                ROUTE_NAME
------------  ---------  ------------------------  ------------
test-start-5  PREPARING  2024-05-11 14:54:25+0800  test-start-5
```

运行`everai worker list`，看到原来在运行的worker正处于`REMOVE`状态，新的worker已经在运行中。  
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
dEXndYfRrpqwAirhBdugYN  RUNNING   REMOVE           2024-05-11 14:54:27+0800
SeaNG9f6hKcQ9J3X93GQEx  RUNNING   FREE             2024-05-11 15:11:37+0800
```

再次使用`curl`执行测试用例，在控制台显示如下的数据信息。可以看到服务器端向客户端持续推送消息已经被更新。  
```bash
curl -H'Authorization: Bearer everai_637wE9obZtmGLyqIJp0lok' https://everai.expvent.com.cn:1111/api/apps/v1/routes/test-start-5/sse
hello world again 0


hello world again 1


hello world again 2


hello world again 3
```

