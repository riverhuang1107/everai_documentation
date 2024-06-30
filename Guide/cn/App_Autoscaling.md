# 应用自动扩缩容

在[新手指引](https://expvent.com/documentation/zh-cn/docs/)中，你已经创建了一个简单的应用。但这个应用的负载超出计算资源的可承受范围时，应用的服务响应会变慢，超时，甚至不可用。[EverAI](https://everai.expvent.com)平台提供了自动扩缩容机制，可以帮助你的应用在高负载的情况下自动扩容，省去你手动部署新计算节点的操作。能够使你的应用能在很短的时间内快速提升负载能力。  

首先，你创建一个`configmap`对象，用于存放自动扩缩容相关的策略参数，示例中的参数定义了最小的worker数量是1，最大的worker数量是5，最大的队列数量是2，扩容worker的步长是1（即每次扩容的worker数量是1）。  
  
```bash
everai configmap create get-start-configmap \ 
  --from-literal min_workers=1 \
  --from-literal max_workers=5 \
  --from-literal max_queue_size=2 \
  --from-literal scale_up_step=1 \
  --from-literal max_idle_time=60
```
基于[新手指引](https://expvent.com/documentation/zh-cn/docs/)中的`app.py`代码，在定义app对象时，需要加入`autoscaler`参数。  
```python
from everai_autoscaler.builtin import SimpleAutoScaler

CONFIGMAP_NAME = 'get-start-configmap'

app = App(
    '<your app name>',
    image=image,
    volume_requests=[
        VolumeRequest(name=VOLUME_NAME, create_if_not_exists=True),
    ],
    secret_requests=[QUAY_IO_SECRET_NAME],
    configmap_requests=[CONFIGMAP_NAME],
    resource_requests=ResourceRequests(
        cpu_num=1,
        memory_mb=1024,
    ),
    autoscaler=SimpleAutoScaler(
        min_workers=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='min_workers'),
        max_workers=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='max_workers'),
        max_queue_size=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='max_queue_size'),
        max_idle_time=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='max_idle_time'),
        scale_up_step=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='scale_up_step'),
    ),
)
```

第二步，你执行`everai app run`确保你的代码运行正常。然后打开`image_builder.py`文件，更新你的镜像版本。并执行`everai image build`进行镜像构建，并推送镜像到[quay.io](https://quay.io/)镜像仓库。镜像构建完成后，你需要执行如下命令进行应用更新。  
```bash
everai app upgrade --image
```

此时，你的应用已经具备了自动扩容的能力。  

执行`everai worker list`可以观察到目前在低负载的情况下，有一个worker在工作。
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
ULSUfqhnsEV35JyuWiuVyo  RUNNING   FREE             2024-05-11 19:00:52+0800
```
执行`everai app queue`可以观察到目前的应用队列中没有在排队的情况。
```bash
  QUEUE_INDEX  CREATE_AT                 QUEUE_REASON
-------------  ------------------------  --------------
```

现在，这一步你可以使用`ab`工具对你的应用进行性能测试，加大你应用的工作负载。并同时观察worker和队列的数量变化。
```bash
ab -s 120 -t 120 -c 4 -n 300000 -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your app route name>/sse
```

在性能测试进行过程中，再次执行`everai worker list`和`everai app queue`，可以看到两者的变化。此时，队列列表中出现在排队的情况。
```bash
  QUEUE_INDEX  CREATE_AT                 QUEUE_REASON
-------------  ------------------------  --------------
            0  2024-05-11 19:25:19+0800  WorkerBusy
            1  2024-05-11 19:25:28+0800  WorkerBusy
```
在worker列表中可以看到目前已经有两个worker在同时工作，性能测试前的只有一个worker在工作。这意味着随着应用负载的加大，[EverAI](https://everai.expvent.com)平台为你的应用自动完成了扩容的工作。  
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
ULSUfqhnsEV35JyuWiuVyo  RUNNING   BUSY             2024-05-11 19:00:52+0800
bqZJ4eTjMmEbP9ncrvPgGg  RUNNING   BUSY             2024-05-11 19:24:08+0800
```

当`ab`性能测试结束，应用业务负载的高峰期过去之后，系统会自动判断worker的负载情况，在设置的`max_idle_time`后，释放掉扩容产生的worker，恢复到设置的`mini_workers`数量。在这个示例中，执行`everai worker list`，可以看到应用的worker数量已经恢复到了扩容前的一个worker。系统已经为你的应用完成了自动缩容操作。 
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
ULSUfqhnsEV35JyuWiuVyo  RUNNING   FREE             2024-05-11 19:00:52+0800
```

