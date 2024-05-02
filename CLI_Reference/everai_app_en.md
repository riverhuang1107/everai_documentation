# everai app
**Usage**:   
```bash
everai app [-h] {create,run,get,upgrade,pause,deploy,prepare,list,ls,queue,q} ...
```

**Positional arguments**:
  {create,run,get,upgrade,pause,deploy,prepare,list,ls,queue,q}
                        App command help  
    `create`              Create an app  
    `run`                 Local run a everai application for test  
    `get`                 Get app information  
    `upgrade`             Upgrade an app  
    `pause`               Pause an app, all worker will be stopped  
    `deploy`              Deploy an app to serving status  
    `pause`               Pause an app, all worker will be stopped  
    `prepare`             Prepare an app, all of function which decorated by @app.prepare would be called  
    `list (ls)`           List all apps  
    `queue (q)`           List queue of app  

**Options**:  
  -h, --help            show this help message and exit

## everai app create             
Create an app
**Examples**:
```bash
everai app create get-start
```

```bash
everai app create get-start --route-name got-started
```

**Usage**: 
```bash 
everai app create [-h] [--route-name ROUTE_NAME] [name]
```

**Positional arguments**:  
  `name`                  The app name  

**Options**:  
-h, --help            show this help message and exit  
--route-name ROUTE_NAME, -r ROUTE_NAME
                        Globally unique route name. By default, it is same with the app name. Once the application name conflicts, route-name needs to be set explicitly.

## everai app run                 
Local run a everai application for test
**Examples**:
```bash
everai app run
```

**Usage**: 
```bash 
everai app run [-h] [--port PORT] [--listen LISTEN]
```

**Options**:  
-h, --help       show this help message and exit
--port PORT      The port to bind to
--listen LISTEN  The interface to bind to

## everai app get                 
Get app information  
**Examples**:
```bash
everai app get get-start
```

**Usage**: 
```bash 
everai app get [-h] name
```

**Positional arguments**:  
  `name`        The app name

**Options**:
-h, --help  show this help message and exit

## everai app upgrade             
Upgrade an app  
## everai app pause               
Pause an app, all worker will be stopped  
## everai app deploy              
Deploy an app to serving status  
**Examples**:  
```bash
 everai app deploy
```

**Usage**:  
```bash
everai app deploy [-h]
```

**Options**:  
-h, --help  show this help message and exit

## everai app prepare             
Prepare an app, all of function which decorated by @app.prepare would be called  
**Examples**:  
Before your application cloud be deployed, you should construct your volume first, if your app use at least one volume.
For production environment, the volumes are very important, you could call the following command to prepare it.
This command line will call all functions who decorated by @app.prepare, in these functions you should set up volume data before the app use it
```bash
everai app prepare
```

**Usage**: 
```bash 
everai app prepare [-h]
```

**Options**:  
-h, --help  show this help message and exit  

## everai app list (ls)           
List all apps  
**Examples**:
```bash
everai app list
```

**Usage**:  
```bash
everai app list [-h] [--output [OUTPUT]]
```

**Options**:  
-h, --help            show this help message and exit  
--output [OUTPUT], -o [OUTPUT]
                        Output format, One of: (json, yaml, table, wide)

##everai app queue (q)           
List queue of app
