# everai app
**格式**:   
```bash
everai app [-h] {create,run,get,upgrade,pause,deploy,prepare,list,ls,queue,q} ...
```

**命令**:  
  * `create`              创建一个应用  
  * `run`                 在本地环境运行应用进行测试  
  * `get`                 获取一个应用的信息  
  * `upgrade`             升级应用  
  * `pause`               停止应用，所有的worker都会停止  
  * `deploy`              部署应用到可用状态  
  * `prepare`             准备应用，所有被`@app.prepare`注解的方法都会被执行  
  * `list (ls)`           显示应用列表  
  * `queue (q)`           显示应用队列  

**选项**:  
  * `-h, --help`            显示帮助信息

## everai app create             
创建一个应用  

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

  * `name`                  应用名称  

**选项**:  
* `-h, --help`            显示帮助信息  
* `--route-name ROUTE_NAME, -r ROUTE_NAME`
                        Globally unique route name. By default, it is same with the app name. Once the application name conflicts, route-name needs to be set explicitly.

## everai app run                 
在本地环境运行应用进行测试  

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
获取一个应用的信息  

**示例**:
```bash
everai app get <your app name>
```

**格式**: 
```bash 
everai app get [-h] name
```

**参数**:  
  * `name`        应用名称

**选项**:
* `-h, --help`  显示帮助信息

## everai app upgrade             
升级应用  

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
停止应用，所有的worker都会停止  

## everai app deploy              
部署应用到可用状态  

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
准备应用，所有被@app.prepare注解的方法都会被执行    

**示例**:  

 如果你的应用需要用到文件对象存储，那么在你的应用部署到云环境之前，你需要先创建一个对象存储。  

在生产环境，对象存储是非常重要的，你可以通过下面的命令准备它。  

这句命令会执行所有被`@app.prepare`注解过的方法，在这些方法中你应该配置好文件数据。  

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
显示应用列表  

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
显示应用队列  

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

 * `app_name`              应用名称

**选项**:  

 * `-h, --help`            显示帮助信息
 * `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table
                        

