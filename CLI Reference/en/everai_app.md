# everai app
Manage app  

**Usage**:   
```bash
everai app [-h] {create,run,get,upgrade,pause,resume,deploy,prepare,list,ls,queue,q,worker,workers,w,check} ...
```

**Commands**:  
  * `create`              Create an app  
  * `run`                 Local run a everai application for test  
  * `get`                 Get app information  
  * `upgrade`             Upgrade an app  
  * `pause`               Pause an app, all worker will be stopped
  * `resume`              Resume an app   
  * `deploy`              Deploy an app to serving status  
  * `pause`               Pause an app, all worker will be stopped  
  * `prepare`             Prepare an app, all of function which decorated by `@app.prepare` would be called  
  * `list (ls)`           List all apps  
  * `queue (q)`           List queue of app  
  * `worker (workers, w)`   Manage the worker of app
  * `check`                 Check whether app is correct in app directory

**Options**:  
  * `-h, --help`            show this help message and exit

## everai app create             
Create an app  

**Example**:
```bash
everai app create <your app name>
```
When you run command `everai app create`，the output shows the error like: `Route path exist`, you can set a new route name to solve this problem.  

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
Locally run a everai application for test  

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
* `--listen LISTEN`  The specific network interface to bind the app's api endpoint service to restrict communication. It can be a specific IP address, for example: 192.168.1.123. Defaults to 0.0.0.0, which supports receiving traffic from any network interface.

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

When you want to update your code, you should update your app image. For detailed guidance on app image upgrade, check out [everai app upgrade --image](https://expvent.com/documentation/docs/Guide/App_Upgrade#everai-app-upgrade---image).  

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

**Example**:
```bash
everai worker list <your app name>
```

Show worker list of app，you can see 2 running workers.  

```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
DFLH3kbnn9gyzzdk8Vypyx  RUNNING   FREE             2024-05-11 12:02:12+0800
KN63GutywVFnutrrQ6wLvw  RUNNING   FREE             2024-05-11 12:02:12+0800
```
Run the following command to stop app of the example.  

```bash
everai app pause <your app name>
```

Show the work list again, you can see nothing in worker list.  

```bash
everai worker list <your app name>

ID    STATUS    DETAIL_STATUS    CREATED_AT    DELETED_AT
----  --------  ---------------  ------------  ------------
```

Run command `everai app list`, you can see that  the status of app is `PAUSED`.  

```bash
NAME          STATUS    CREATED_AT                ROUTE_NAME
------------  --------  ------------------------  ------------
test-start-4  PAUSED    2024-05-11 14:54:25+0800  test-start-4
```

**Usage**:  
```bash
everai app pause [-h] [name]
```

**Positional arguments**:  

  * `name`        The app name 

**Options**:
  * `-h, --help`  show this help message and exit 

## everai app resume           
Resume an app  

**Example**:

When you want to resume an application that has been stopped (the application status is `PAUSED`), you can execute the following command:  

```bash
everai app resume <your app name>
```

**Usage**:  
```bash
everai app resume [-h] [name]
```

**Positional arguments**:  

  * `name`        The app name 

**Options**:  
* `-h, --help`            show this help message and exit  

## everai app deploy              
Deploy an app to serving status  

**Example**:  
```bash
 everai app deploy
```
After running `everai app list`, you can see your app's status. If status is `DEPLOYED`, it means that your app is deployed successfully.   
```bash
NAME          STATUS    CREATED_AT                ROUTE_NAME
------------  --------  ------------------------  ------------
get-start     DEPLOYED  2024-04-29 15:05:18+0800  got-started
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
NAME          STATUS    CREATED_AT                ROUTE_NAME
------------  --------  ------------------------  ------------
get-start     DEPLOYED  2024-04-29 15:05:18+0800  got-started
test-start-2  DEPLOYED  2024-05-06 18:36:35+0800  test-start-2
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
-------------  ------------------------  --------------
            0  2024-05-07 19:26:12+0800  WorkerBusy
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

## everai app worker (workers, w)
Manage the worker of app  

**Usage**:
```bash 
everai app worker [-h] {list,ls} ...
```

**Commands**:
  * `list (ls)`    List workers of app 

**Options**:
  * `-h, --help`  show this help message and exit 

### everai app worker (workers, w) list (ls)

List workers of app  

**Example**:
```bash
everai app worker list <your app name>
```
The output for this example is this:  

```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
UskkMtU5HihJhiyWevc8We  RUNNING   FREE             2024-05-14 09:47:54+0800
```
If the app name is not attached, output shows the list of workers in current app directory.  

```bash
everai app worker list
```
The output for this example is this:  
  
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
NwKKpZHesYBUncaw4bZdYz  RUNNING   FREE             2024-05-14 12:59:04+0800
```

**Usage**:  
```bash 
everai app worker list [-h] [--output [OUTPUT]] [--all] [--recent-days [RECENT_DAYS]] [app_name]
```

**Positional arguments**:  
  * `app_name`              The app name

**Options**:
  * `-h, --help`            show this help message and exit 
  * `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide) 
  * `--all, -a`             show all workers, include deleted and errors 
  * `--recent-days` [RECENT_DAYS], -d [RECENT_DAYS]
                        show not running workers who is created in recent days 

## everai app check                 
Check whether app is correct in app directory  

**Example**:
```bash
everai app check
```
If the code in the application directory has syntax errors, the following output will appear:    

```bash
find object in app.py: 'Service' object has no attribute 'rout'
```

**Usage**: 
```bash 
everai app check [-h]
```

**Options**:  
* `-h, --help`       show this help message and exit
