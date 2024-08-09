# 常见问题

## 创建或者更新应用时，如何查看应用部署进度？
创建或者更新应用时，整个部署过程会涉及到多个步骤，如拉取镜像，拉取数据卷，创建worker容器等等。你可以通过执行`everai app event`命令查看当前应用的部署进度。  

```bash
everai app event <your app name>
```

输出的结果如下所示，`CREATED_AT`使用UTC时间显示。

```bash
TYPE    FROM       CREATED_AT                MESSAGE
------  ---------  ------------------------  ------------------------------------------------------------------------------------------------
NORMAL  AppSever   2024-07-06T14:57:29+0000  Worker/GcaPycCfyW3XEBuwNYaXZ9 is ready now
NORMAL  Scheduler  2024-07-06T14:57:18+0000  Successfully assigned worker/GcaPycCfyW3XEBuwNYaXZ9 to node/5a684c93-84c0-4078-821c-a4aeccb61407
NORMAL  AppSever   2024-07-06T14:57:06+0000  Successfully deployed app
```

## 应用是否可以挂载多个数据卷？
支持。应用支持挂载多个数据卷。

### Manifest Mode
如果你的应用通过manifest模式创建，你可以在manifest文件中定义你的多个数据卷。  

```yaml
...
spec:
  ...
  volumeMounts:
    - name: get-start-volume                                    # volume name
      mountPath: /workspace/volume       # mount path in container
      readOnly: true                              # only support `readOnly = true` currently, default is true
    - name: test-start-volume                                    # volume name
      mountPath: /workspace/volume-test       # mount path in container
      readOnly: true                              # only support `readOnly = true` currently, default is true
  
  volumes:                                        # optional field, but very important for AI app
    - name: get-start-volume                                    # volume name
      volume: 
        volume: get-start-volume          # use a private volume or a public volume from other user
    - name: test-start-volume                                    # volume name
      volume:
        volume: test-start-volume          # use a a private volume or a public volume from other user
  ...
```

### App Object Mode
如果你的应用通过app object模式创建，你可以在`app.py`文件中挂载你的多个数据卷。  

```python
VOLUME_NAME = 'get-start-volume'
VOLUME_NAME_2 = 'test-start-volume'

app = App(
    ...
    volume_requests=[
        VolumeRequest(name=VOLUME_NAME),
        VolumeRequest(name=VOLUME_NAME_2)
    ]
    ...
)
```

## 应用部署到EverAI平台后，是否支持在worker容器中运行命令？
支持。你可以通过命令行工具指令`everai app worker exec`在运行中的worker容器中运行命令。

举例来说，当你想访问worker容器的bash shell环境，可以执行如下命令：  

```bash 
everai worker exec -it dweBRSPD395BvtBDsZYum8 bash
```

命令执行后，当前的终端环境已经进入到worker容器内`workspace`目录下。  

```bash 
root@b0b6f096aeab:/workspace#
```

`everai app worker exec`的详细使用方法请参考[everai app worker exec](https://expvent.com/documentation/zh-cn/docs/CLI%20Reference/everai_app#everai-app-worker-exec)。

## 是否支持修改数据卷在本地环境的存放路径？
支持。首先可以通过`everai config`查看EverAI平台当前的环境变量，其中包括数据卷在本地环境的存放路径。  

```bash
EVERAI_HOME /root/.cache/everai
EVERAI_ENDPOINT https://everai.expvent.com
EVERAI_VOLUME_ROOT /root/.cache/everai/volumes
```

* **Linux(WSL) & MacOS**  

执行如下命令，可以自定义本地环境中的数据卷存放路径。为了避免每次使用时设置该环境变量，我们可以把该命令加入到`.bashrc`，并且执行`source`命令，使之立即生效。  

```bash
export EVERAI_VOLUME_ROOT=/data/
```
再次执行`everai config`，可以看到`EVERAI_VOLUME_ROOT`的存放路径已经修改完成。  

```bash
EVERAI_HOME /root/.cache/everai
EVERAI_ENDPOINT https://everai.expvent.com
EVERAI_VOLUME_ROOT /data/
```

* **Windows PowerShell**

你可以在系统属性的环境变量中添加一个变量`EVERAI_VOLUME_ROOT`，并设置该变量的值为本地环境中的数据卷存放路径地址。

执行`everai config`，可以看到`EVERAI_VOLUME_ROOT`的存放路径已经修改完成。
```powershell
EVERAI_HOME C:\Users\<Username>\.cache\everai
EVERAI_ENDPOINT https://everai.expvent.com
EVERAI_VOLUME_ROOT D:\data
```