# everai app
**格式**:   
```bash
everai app [-h] {create,run,get,upgrade,pause,deploy,prepare,list,ls,queue,q} ...
```

**命令**:  
  * `create`              Create an app  
  * `run`                 Local run a everai application for test  
  * `get`                 Get app information  
  * `upgrade`             Upgrade an app  
  * `pause`               Pause an app, all worker will be stopped  
  * `deploy`              Deploy an app to serving status  
  * `pause`               Pause an app, all worker will be stopped  
  * `prepare`             Prepare an app, all of function which decorated by `@app.prepare` would be called  
  * `list (ls)`           List all apps  
  * `queue (q)`           List queue of app  

**选项**:  
  * `-h, --help`            显示帮助信息

## everai app create             
Create an app  

**示例**:
```bash
everai app create <your app name>
```

```bash
everai app create <your app name> --route-name <your app route name>
```

**格式**: 
```bash 
everai app create [-h] [--route-name ROUTE_NAME] [name]
```

**参数**:  

  * `name`                  The app name  

**选项**:  
* `-h, --help`            显示帮助信息  
* `--route-name ROUTE_NAME, -r ROUTE_NAME`
                        Globally unique route name. By default, it is same with the app name. Once the application name conflicts, route-name needs to be set explicitly.

## everai app run                 
Local run a everai application for test  

**示例**:
```bash
everai app run
```

**格式**: 
```bash 
everai app run [-h] [--port PORT] [--listen LISTEN]
```

**选项**:  
* `-h, --help`       显示帮助信息
* `--port PORT`      The port to bind to
* `--listen LISTEN`  The interface to bind to

## everai app get                 
Get app information  

**示例**:
```bash
everai app get <your app name>
```

**格式**: 
```bash 
everai app get [-h] name
```

**参数**:  
  * `name`        The app name

**选项**:
* `-h, --help`  显示帮助信息

## everai app upgrade             
Upgrade an app  

**示例**:
```bash
everai app upgrade --image
```

**格式**: 
```bash
everai app upgrade [-h] [--autoscaling-policy] [--resource-requests] [--volume-requests] [--secret-requests] [--image] [--all]
```

**选项**:  

  * `-h, --help`            显示帮助信息
  * `--autoscaling-policy`  Upgrade the autoscaling policy only
  * `--resource-requests`   Upgrade the resource requests only, this operation will trigger the worker rollout
  * `--volume-requests`     Upgrade the volume requests only, this operation will trigger the worker rollout
  * `--secret-requests`     Upgrade the secret requests only, this operation will trigger the worker rollout
  * `--image`               Upgrade the image only, this operation will trigger the worker rollout
  * `--all`                 Upgrade all of the settings, this operation will trigger the worker rollout

## everai app pause               
Pause an app, all worker will be stopped  

## everai app deploy              
Deploy an app to serving status  

**示例**:  
```bash
 everai app deploy
```
After running `everai app list`, you can see your app's status. If status is `DEPLOYED`, it means that your app is deployed successfully.   
```bash
NAME          STATUS    CREATED_AT                ROUTE_NAME
------------  --------  ------------------------  ------------
get-start     DEPLOYED  2024-04-29 15:05:18+0800  got-started
```
**格式**:  
```bash
everai app deploy [-h]
```

**选项**:  
* `-h, --help`  显示帮助信息

## everai app prepare             
Prepare an app, all of function which decorated by `@app.prepare` would be called  

**示例**:  

Before your application is deployed in the cloud, you should construct your volume first, if your app uses at least one volume.  

For production environment, the volumes are very important, you could call the following command to prepare it.  

This command line will call all functions which are decorated by `@app.prepare`, in these functions you should set up volume data before the app use it.  
```bash
everai app prepare
```

**格式**: 
```bash 
everai app prepare [-h]
```

**选项**:  
* `-h, --help`  显示帮助信息  

## everai app list (ls)           
List all apps  

**示例**:
```bash
everai app list
```
The result could be shown like this:  
```bash
NAME          STATUS    CREATED_AT                ROUTE_NAME
------------  --------  ------------------------  ------------
get-start     DEPLOYED  2024-04-29 15:05:18+0800  got-started
test-start-2  DEPLOYED  2024-05-06 18:36:35+0800  test-start-2
```
**格式**:  
```bash
everai app list [-h] [--output [OUTPUT]]
```

**选项**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table

## everai app queue (q)           
List queue of app  

**示例**:  
```bash
everai app queue <your app name>
```
The output for this example is this: 
```bash
  QUEUE_INDEX  CREATE_AT                 QUEUE_REASON
-------------  ------------------------  --------------
            0  2024-05-07 19:26:12+0800  WorkerBusy
```

**格式**: 
```bash
everai app queue [-h] [--output [OUTPUT]] [app_name]
```

**参数**:  

 * `app_name`              The app name

**选项**:  

 * `-h, --help`            显示帮助信息
 * `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table
                        

