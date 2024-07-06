# everai app
Manage app  

**Usage**:   
```bash
everai app [-h] {create,run,get,update,pause,resume,prepare,list,ls,queue,q,events,e,event,worker,workers,w,check,delete} ...
```

**Commands**:  
  * `create`              Create an app  
  * `run`                 Local run a everai application for test  
  * `get`                 Get app information  
  * `update`              Update an app  
  * `pause`               Pause an app, all worker will be stopped
  * `resume`              Resume an app   
  * `prepare`             Prepare an app, all of function which decorated by `@app.prepare` would be called  
  * `list (ls)`           List all apps  
  * `queue (q)`           List queue of app  
  * `events (e, event)`     List events of app
  * `worker (workers, w)`   Manage the worker of app
  * `check`                 Check whether app is correct in app directory
  * `delete`              Delete an app

**Options**:  
  * `-h, --help`            show this help message and exit

## everai app create             
Create an app from manifest file or an App object in `app.py`.

`--from-file` indicates a manifest file for create app, otherwise, everai command line tool finds app setup in `app.py`.  

**Example**:
```bash
everai app create
```

**Usage**: 
```bash 
everai app create [-h] [--dry-run] [-f FROM_FILE] [-n NAMESPACE]
```

**Options**:  
* `-h, --help`            show this help message and exit  
* `--dry-run`             dry run in client
* `-n NAMESPACE, --namespace NAMESPACE`
                        indicate namespace of the app, commandline > [yaml file | `app.py`] > default
* `-f FROM_FILE, --from-file FROM_FILE`
                        Create app from manifest file (format in yaml), for example: `--from-file filename`

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
everai app get [-h] [--namespace [NAMESPACE]] [--output [OUTPUT]] name
```

**Positional arguments**:  
  * `name`        The app name

**Options**:
* `-h, --help`  show this help message and exit
* `--namespace [NAMESPACE], -n [NAMESPACE]`
                        The namespace of app
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)

## everai app update             
Update an app from manifest file or an App object in `app.py`.

`--from-file` indicates a manifest file for update app, otherwise, everai command line tool finds App in `app.py`.  

This operation may be trigger the worker rollout, if image, command ... changed.

For detailed guidance on app update, check out [App Update](https://expvent.com/documentation/docs/Guide/App_Upgrade).
  
**Example**:  

```bash
everai app update
```

**Usage**: 
```bash
everai app update [-h] [--dry-run] [-f FROM_FILE]
```

**Options**:  

  * `-h, --help`            show this help message and exit
  * `--dry-run`             dry run in client
  * `-f FROM_FILE, --from-file FROM_FILE`
                        Update app from manifest file (format in yaml), for example: `--from-file <filename>`

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
8UtmxyvQ4qC67awQMKDgJb  RUNNING   FREE             2024-06-26T08:54:23+0000
Q6FRr6sRVPwFyyMfDCCEFY  RUNNING   FREE             2024-06-26T08:54:23+0000
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

Run command `everai app list`, you can see that the status of app is `PAUSED`.  

```bash
NAME                             NAMESPACE    STATUS    WORKERS    CREATED_AT
-------------------------------  -----------  --------  ---------  ------------------------
test-start-14                    default      PAUSED    0/0        2024-06-18T08:49:30+0000
```

**Usage**:  
```bash
everai app pause [-h] [-n [NAMESPACE]] [--dry-run] name
```

**Positional arguments**:  

  * `name`        The app name 

**Options**:
  * `-h, --help`  show this help message and exit 
  * `-n [NAMESPACE], --namespace [NAMESPACE]`
                        The namespace of app
  * `--dry-run`             dry run in client

## everai app resume           
Resume an app  

**Example**:

When you want to resume an application that has been stopped (the application status is `PAUSED`), you can execute the following command:  

```bash
everai app resume <your app name>
```

**Usage**:  
```bash
everai app resume [-h] [-n [NAMESPACE]] name
```

**Positional arguments**:  

  * `name`        The app name 

**Options**:  
* `-h, --help`            show this help message and exit
* `-n [NAMESPACE], --namespace [NAMESPACE]`
                        The namespace of app  

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
NAME       NAMESPACE    STATUS    WORKERS    CREATED_AT
---------  -----------  --------  ---------  ------------------------
get-start  default      DEPLOYED  3/3        2024-06-18T04:21:37+0000
```
**Usage**:  
```bash
everai app list [-h] [-n NAMESPACE] [-A] [--output [OUTPUT]]
```

**Options**:  
* `-h, --help`            show this help message and exit  
* `-n NAMESPACE, --namespace NAMESPACE`
                        List all apps in specified namespaces
* `-A, --all-namespaces`  List all apps in all namespaces
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
            0  2024-07-03T22:24:07+0000  WorkerBusy
            1  2024-07-03T22:24:07+0000  WorkerBusy
```

**Usage**: 
```bash
everai app queue [-h] [-n [NAMESPACE]] [--output [OUTPUT]] name
```

**Positional arguments**:  

 * `app_name`              The app name

**Options**:  

 * `-h, --help`            show this help message and exit
 * `-n [NAMESPACE], --namespace [NAMESPACE]`
                        The namespace of app  
 * `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)

## everai app event           
List events of app  

**Example**:  
```bash
everai app event <your app name>
```
The output for this example is this: 
```bash
TYPE    FROM       CREATED_AT                MESSAGE
------  ---------  ------------------------  ------------------------------------------------------------------------------------------------
NORMAL  AppSever   2024-06-25T12:27:05+0000  Worker/oFHDnZHrDMo5WHnQkhcegx is ready now
NORMAL  AppSever   2024-06-25T12:27:05+0000  Worker/jQd38Z5HwRLv24x459CSBG is ready now
NORMAL  Scheduler  2024-06-25T12:26:59+0000  Successfully assigned worker/oFHDnZHrDMo5WHnQkhcegx to node/5a684c93-84c0-4078-821c-a4aeccb61407
NORMAL  Scheduler  2024-06-25T12:26:59+0000  Successfully assigned worker/jQd38Z5HwRLv24x459CSBG to node/5a684c93-84c0-4078-821c-a4aeccb61407
```

**Usage**: 
```bash
everai app events [-h] [-n [NAMESPACE]] [--output [OUTPUT]] name
```

**Positional arguments**:  

 * `name`              The app name

**Options**:  

 * `-h, --help`            show this help message and exit
 * `-n [NAMESPACE], --namespace [NAMESPACE]` The namespace of app
 * `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)


## everai app worker (workers, w)
Manage the worker of app  

**Usage**:
```bash 
everai app worker [-h] {list,ls,exec} ...
```

**Commands**:
  * `list (ls)`    List workers of app 
  * `exec`          Run a command in a running worker


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
4gexdRcjZyAwPGNeNFNHpr  RUNNING   FREE             2024-06-25T12:19:05+0000
mHFmmq7fJWWtERAyFDLrcA  RUNNING   FREE             2024-06-25T12:19:05+0000
```
If the app name is not attached, everai command line tool finds app name in `app.py` which is in current app directory.    

```bash
everai app worker list
```
The output for this example is this:  
  
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
8UtmxyvQ4qC67awQMKDgJb  RUNNING   FREE             2024-06-26T08:54:23+0000
```

**Usage**:  
```bash 
everai app worker list [-h] [--output [OUTPUT]] [--all] [--recent-days [RECENT_DAYS]] [--namespace [NAMESPACE]] [app_name]
```

**Positional arguments**:  
  * `app_name`              The app name

**Options**:
  * `-h, --help`            show this help message and exit 
  * `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide) 
  * `--all, -a`             show all workers, include deleted and errors 
  * `--recent-days [RECENT_DAYS], -d [RECENT_DAYS]`
                        show not running workers who is created in recent days
  * `--namespace [NAMESPACE], -n [NAMESPACE]`
                        namespace of app 

### everai app worker exec

Run a command in a running worker  

**Example**:  
```bash 
everai app worker exec -it dweBRSPD395BvtBDsZYum8 bash
```

The result could be shown like this:  

```bash 
root@b0b6f096aeab:/workspace#
```

**Usage**:  
```bash  
everai app worker exec [-h] [--interactive] [--tty] worker command ...
```

**Positional arguments**:  
  * `worker`             worker id
  * `command`            command
  * `args`               arguments for command in worker

**Options**:  
* `-h, --help`            show this help message and exit  
* `--interactive, -i`  Keep STDIN open even if not attached
* `--tty, -t`          Allocate a pseudo-TTY, just for compatible client, no effect

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

## everai app delete                 
Delete an app  

**Example**:
```bash
everai app delete <your app name>
```

**Usage**: 
```bash 
everai app delete [-h] [--namespace [NAMESPACE]] [--force] name
```

**Options**:  
* `-h, --help`       show this help message and exit
* `--namespace [NAMESPACE], -n [NAMESPACE]`
                        The namespace of app
* `--force`