# 应用按需运行

在[EverAI](https://everai.expvent.com)平台，你只需要为你真正在使用的计算资源时长支付费用。你不再需要为业务空闲时的资源支付任何费用。通过合理使用`everai app pause`和`everai app resume`即可达成这个目标。  


执行如下的命令，停止示例中的应用。停止应用，所有的worker都会停止。  
```bash
everai app pause <your app name>
```

执行`everai worker list <your app name>`，查看应用的worker列表，可以看到之前在运行的worker已经被停止。  
```bash
ID    STATUS    DETAIL_STATUS    CREATED_AT    DELETED_AT
----  --------  ---------------  ------------  ------------
```

执行`everai app list`，可以看到此时应用的状态是`PAUSED`。  
```bash
NAME                             NAMESPACE    STATUS    WORKERS    CREATED_AT
-------------------------------  -----------  --------  ---------  ------------------------
test-start-004                   default      PAUSED    0/0        2024-06-28T05:27:24+0000
```

worker被停止后，你的应用的计费也会被随之停止。根据你的实际业务需要，当你再次部署该应用后，应用会被系统继续计费。  
```bash
 everai app resume <your app name>
```

执行`everai app list`后，可以看到类似如下的输出结果。如果你的应用状态是`DEPLOYED`，意味着你的应用已经被再次部署。  
```bash
NAME                             NAMESPACE    STATUS    WORKERS    CREATED_AT
-------------------------------  -----------  --------  ---------  ------------------------
test-start-004                   default      DEPLOYED  1/1        2024-06-28T05:27:24+0000
```


