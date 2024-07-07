# 常见问题

## 创建或者更新应用时，如何查看应用部署进度？
创建或者更新应用时，整个部署过程会涉及到多个步骤，如拉取镜像，拉取数据卷，创建worker容器等等。你可以通过执行`everai app event`命令查看当前应用的部署进度。  

```bash
everai app event <your app name>
```

输出的结果如下所示：

```bash
TYPE    FROM       CREATED_AT                MESSAGE
------  ---------  ------------------------  ------------------------------------------------------------------------------------------------
NORMAL  AppSever   2024-07-06T14:57:29+0000  Worker/GcaPycCfyW3XEBuwNYaXZ9 is ready now
NORMAL  Scheduler  2024-07-06T14:57:18+0000  Successfully assigned worker/GcaPycCfyW3XEBuwNYaXZ9 to node/5a684c93-84c0-4078-821c-a4aeccb61407
NORMAL  AppSever   2024-07-06T14:57:06+0000  Successfully deployed app
```