# everai app
**Usage**:   
```bash
everai app [-h] {create,run,get,upgrade,pause,deploy,prepare,list,ls,queue,q} ...
```

**Commands**:  
  * `create`              Create an app  
  * `run`                 Local run a everai application for test  
  * `get`                 Get app information  
  * `upgrade`             Upgrade an app  
  * `pause`               Pause an app, all worker will be stopped  
  * `deploy`              Deploy an app to serving status  
  * `pause`               Pause an app, all worker will be stopped  
  * `prepare`             Prepare an app, all of function which decorated by @app.prepare would be called  
  * `list (ls)`           List all apps  
  * `queue (q)`           List queue of app  

**Options**:  
  * `-h, --help`            show this help message and exit

## everai app create             
Create an app  

**Example**:
```bash
everai app create <your app name>
```

```bash
everai app create <your app name> --route-name <your app route name>
```

**Usage**: 
```bash 
everai app create [-h] [--route-name ROUTE_NAME] [name]
```

**Positional arguments**:  

  * `name`                  The app name  

**Options**:  
* `-h, --help`            show this help message and exit  
* `--route-name ROUTE_NAME, -r ROUTE_NAME`
                        Globally unique route name. By default, it is same with the app name. Once the application name conflicts, route-name needs to be set explicitly.

## everai app run                 
Local run a everai application for test  

**Example**:
```bash
everai app run
```

**Usage**: 
```bash 
everai app run [-h] [--port PORT] [--listen LISTEN]
```

**Options**:  
* `-h, --help`       show this help message and exit
* `--port PORT`      The port to bind to
* `--listen LISTEN`  The interface to bind to

## everai app get                 
Get app information  

**Example**:
```bash
everai app get <your app name>
```

**Usage**: 
```bash 
everai app get [-h] name
```

**Positional arguments**:  
  * `name`        The app name

**Options**:
* `-h, --help`  show this help message and exit

## everai app upgrade             
Upgrade an app  

**Example**:
```bash
everai app upgrade --image
```

**Usage**: 
```bash
everai app upgrade [-h] [--autoscaling-policy] [--resource-requests] [--volume-requests] [--secret-requests] [--image] [--all]
```

**Options**:  

  * `-h, --help`            show this help message and exit
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

**Example**:  
```bash
 everai app deploy
```
After running `everai app list`, you can see your app's status. If status is `STATUS_DEPLOYED`, it means that your app is deployed successfully.   
```bash
NAME          STATUS           CREATED_AT                ROUTE_NAME
------------  ---------------  ------------------------  ------------
get-start     STATUS_DEPLOYED  2024-04-29 15:05:18+0800  got-started
```
**Usage**:  
```bash
everai app deploy [-h]
```

**Options**:  
* `-h, --help`  show this help message and exit

## everai app prepare             
Prepare an app, all of function which decorated by `@app.prepare` would be called  

**Example**:  

Before your application is deployed in the cloud, you should construct your volume first, if your app uses at least one volume.  

For production environment, the volumes are very important, you could call the following command to prepare it.  

This command line will call all functions which are decorated by `@app.prepare`, in these functions you should set up volume data before the app use it.  
```bash
everai app prepare
```

**Usage**: 
```bash 
everai app prepare [-h]
```

**Options**:  
* `-h, --help`  show this help message and exit  

## everai app list (ls)           
List all apps  

**Example**:
```bash
everai app list
```
The result could be shown like this:  
```bash
NAME          STATUS           CREATED_AT                ROUTE_NAME
------------  ---------------  ------------------------  ------------
get-start     STATUS_DEPLOYED  2024-04-29 15:05:18+0800  got-started
get-started   STATUS_INIT      2024-04-29 15:02:40+0800  get-started
```
**Usage**:  
```bash
everai app list [-h] [--output [OUTPUT]]
```

**Options**:  
* `-h, --help`            show this help message and exit  
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)

## everai app queue (q)           
List queue of app  

**Example**:  
```bash
everai app queue <your app name>
```
The output for this example is this: 
```bash
  QUEUE_INDEX  CREATE_AT                 QUEUE_REASON
-------------  ------------------------  ---------------------
            0  2024-05-07 10:30:00+0800  QueueReasonWorkerBusy
```

**Usage**: 
```bash
everai app queue [-h] [--output [OUTPUT]] [app_name]
```

**Positional arguments**:  

 * `app_name`              The app name

**Options**:  

 * `-h, --help`            show this help message and exit
 * `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)
                        

