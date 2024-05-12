# App Autoscaling

In the `quickstart`, we have created a simple application. But when the load of this application exceeds the endurance range of computing resources, the application's service response will slow down, time out, or even be unavailable. The [EverAI](https://everai.expvent.com) platform provides an autoscaling mechanism that can help your application automatically expand under high load conditions, eliminating the need for you to manually deploy new computing nodes. This enables your application to quickly increase its load capacity in a short period of time.    

First you create a `configmap` by the following command, this `configmap` includes policy parameters about autoscaling, the example defines that the mini workers number is 1, the max workers number is 5, and the max queue size is 2.  

```bash
ever configmap create get-start-configmap \ 
  --from-literal min_workers=1 \
  --from-literal max_workers=5 \
  --from-literal max_queue_size=2 \
  --from-literal scale_up_step=1 \
  --from-literal max_idle_time=60
```
Based on the `app.py` code in `quickstart`, When you define tha object of app,  you should add parameter `autoscaling_policy`.  

```bash
from everai.autoscaling import SimpleAutoScalingPolicy

CONFIGMAP_NAME = 'get-start-configmap'

app = App(
    'get-start',
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
    autoscaling_policy=SimpleAutoScalingPolicy(
        min_workers=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='min_workers'),
        max_workers=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='max_workers'),
        max_queue_size=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='max_queue_size'),
        max_idle_time=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='max_idle_time'),
        scale_up_step=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='scale_up_step'),
    ),
)
```
Secondly, you run `everai app run` to check your code locally. And then open  `image_builder.py`, update your image registry's version. Run `everai image build` to buld image and push the image to [quay.io](https://quay.io/). Aftering build image, you can run the following command to upgrade your app.  

```bash
everai app upgrade --image
```

此时，你的应用已经具备了自动扩容的能力。执行`everai worker list`可以观察到目前在低负载的情况下，有一个worker在工作。
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
ULSUfqhnsEV35JyuWiuVyo  RUNNING   BUSY             2024-05-11 19:00:52+0800
```
执行`everai app queue`可以观察到目前的应用队列中没有在排队的情况。
```bash
  QUEUE_INDEX  CREATE_AT                 QUEUE_REASON
-------------  ------------------------  --------------
```

现在，这一步你可以使用ab工具对你的应用进行性能测试，加大你应用的工作负载。并同时观察worker和队列的数量变化。
```bash
ab -s 120 -t 120 -c 4 -n 300000 -H'Authorization: Bearer everai_637wE9obZtmGLyqIJp0lok' https://everai.expvent.com.cn:1111/api/apps/v1/routes/test-start-6/sse
```

在性能测试进行过程中，再次执行执行`everai worker list`和`everai app queue`，可以看到两者的变化。此时，队列列表中出现在排队的情况。
```bash
  QUEUE_INDEX  CREATE_AT                 QUEUE_REASON
-------------  ------------------------  --------------
            0  2024-05-11 19:25:19+0800  WorkerBusy
            1  2024-05-11 19:25:28+0800  WorkerBusy
```
在worker列表中可以看到目前已经有两个worker在同时工作，性能测试前的只有一个worker在工作。这意味着随着应用负载的加大，EverAI平台为你的应用自动完成了扩容的工作。  
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
ULSUfqhnsEV35JyuWiuVyo  RUNNING   BUSY             2024-05-11 19:00:52+0800
bqZJ4eTjMmEbP9ncrvPgGg  RUNNING   BUSY             2024-05-11 19:24:08+0800
```


