# FAQ

## How to check the application deployment progress when creating or updating an application?

When creating or updating an application, the entire deployment process involves multiple steps, such as pulling images, pulling data volumes, creating worker containers, etc. You can view the deployment progress of the current application by executing the `everai app event` command.

```bash
everai app event <your app name>
```

The output results are as follows:

```bash
TYPE    FROM       CREATED_AT                MESSAGE
------  ---------  ------------------------  ------------------------------------------------------------------------------------------------
NORMAL  AppSever   2024-07-06T14:57:29+0000  Worker/GcaPycCfyW3XEBuwNYaXZ9 is ready now
NORMAL  Scheduler  2024-07-06T14:57:18+0000  Successfully assigned worker/GcaPycCfyW3XEBuwNYaXZ9 to node/5a684c93-84c0-4078-821c-a4aeccb61407
NORMAL  AppSever   2024-07-06T14:57:06+0000  Successfully deployed app
```

## Can an application mount multiple data volumes?

Support. The application supports mounting multiple data volumes.

If your application is created in manifest mode, you can define your multiple data volumes in the manifest file.  

```yaml
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
```
