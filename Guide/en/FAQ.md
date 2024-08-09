# FAQ

## How to check the application deployment progress when creating or updating an application?

When creating or updating an application, the entire deployment process involves multiple steps, such as pulling images, pulling data volumes, creating worker containers, etc. You can view the deployment progress of the current application by executing the `everai app event` command.

```bash
everai app event <your app name>
```

The output results are as follows, and `CREATED_AT` uses UTC time display.

```bash
TYPE    FROM       CREATED_AT                MESSAGE
------  ---------  ------------------------  ------------------------------------------------------------------------------------------------
NORMAL  AppSever   2024-07-06T14:57:29+0000  Worker/GcaPycCfyW3XEBuwNYaXZ9 is ready now
NORMAL  Scheduler  2024-07-06T14:57:18+0000  Successfully assigned worker/GcaPycCfyW3XEBuwNYaXZ9 to node/5a684c93-84c0-4078-821c-a4aeccb61407
NORMAL  AppSever   2024-07-06T14:57:06+0000  Successfully deployed app
```

## Can an application mount multiple data volumes?

Support. The application supports mounting multiple data volumes.

### Manifest Mode
If your application is created in manifest mode, you can define your multiple data volumes in the manifest file.  

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
If your application is created in app object mode, you can define your multiple data volumes in the `app.py` file.  

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

## After an application is deployed to the EverAI platform, does it support running commands in the worker container?

Support. You can run commands in a running worker container through the command line tool command `everai app worker exec`.

For example, when you want to access the bash shell environment of the worker container, you can execute the following command:

```bash 
everai worker exec -it dweBRSPD395BvtBDsZYum8 bash
```

After the command is executed, the current terminal environment has entered the `workspace` directory in the worker container.

```bash 
root@b0b6f096aeab:/workspace#
```

For detailed usage of `everai app worker exec`, please refer to [everai app worker exec](https://expvent.com/documentation/docs/CLI%20Reference/everai_app#everai-app-worker-exec)。

## Is it supported to modify the storage path of the data volume in the local environment?

Support. First, you can view the current environment variables of the EverAI platform through `everai config`, including the storage path of the data volume in the local environment.

```bash
EVERAI_HOME /root/.cache/everai
EVERAI_ENDPOINT https://everai.expvent.com
EVERAI_VOLUME_ROOT /root/.cache/everai/volumes
```

* **Linux(WSL) & MacOS**  

Execute the following command to customize the data volume storage path in the local environment. In order to avoid setting this environment variable every time it is used, we can add this command to `.bashrc` and execute the `source` command to make it effective immediately.

```bash
export EVERAI_VOLUME_ROOT=/data/
```

Execute `everai config` again, you can see that the storage path of `EVERAI_VOLUME_ROOT` has been modified.

```bash
EVERAI_HOME /root/.cache/everai
EVERAI_ENDPOINT https://everai.expvent.com
EVERAI_VOLUME_ROOT /data/
```
* **Windows PowerShell**

You can add a variable `EVERAI_VOLUME_ROOT` in the environment variables of the system properties, and set the value of this variable to the data volume storage path address in the local environment.

Execute `everai config` again, you can see that the storage path of `EVERAI_VOLUME_ROOT` has been modified.

```powershell
EVERAI_HOME C:\Users\<Username>\.cache\everai
EVERAI_ENDPOINT https://everai.expvent.com
EVERAI_VOLUME_ROOT D:\data
```