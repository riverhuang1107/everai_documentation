# everai app
应用管理  

**格式**:   
```bash
everai app [-h] {create,run,get,update,pause,resume,prepare,list,ls,queue,q,events,e,event,worker,workers,w,check,delete} ...
```

**命令**:  
  * `create`              创建一个应用  
  * `run`                 在本地环境运行应用进行测试  
  * `get`                 获取一个应用的信息  
  * `update`              更新应用  
  * `pause`               停止应用，所有的worker都会停止
  * `resume`              恢复使用应用  
  * `prepare`             准备应用，所有被`@app.prepare`注解的方法都会被执行  
  * `list (ls)`           显示应用列表  
  * `queue (q)`           显示应用队列  
  * `events (e, event)`   显示应用事件
  * `worker (workers, w)` 管理应用的worker 
  * `check`               检查应用目录下的代码编译是否通过
  * `delete`              删除应用

**选项**:  
  * `-h, --help`            显示帮助信息

## everai app create             
通过manifest文件或者在`app.py`中创建App对象来创建一个应用。  

**示例**:
```bash
everai app create
```

**格式**: 
```bash 
everai app create [-h] [--dry-run] [-f FROM_FILE] [-n NAMESPACE]
```

**选项**:  
* `-h, --help`            显示帮助信息  
* `--dry-run`
* `-n NAMESPACE, --namespace NAMESPACE`
                        为应用指定命名空间，指定优先级：命令行 > [manifest文件 | `app.py`] > `default`
* `-f FROM_FILE, --from-file FROM_FILE`
                        通过manifest文件（yaml格式）创建应用，示例：`--from-file <filename>`

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
* `--port PORT`      指定服务端口，默认为80
* `--listen LISTEN`  通过设置特定的网络接口，绑定应用的API服务，用以限制通信。网络接口可以是一个具体的IP地址，例如：192.168.1.123。默认为0.0.0.0，支持从任何网络接口接收流量。

## everai app get                 
获取一个应用的信息  

**示例**:
```bash
everai app get <your app name>
```

**格式**: 
```bash 
everai app get [-h] [--namespace [NAMESPACE]] [--output [OUTPUT]] name
```

**参数**:  
  * `name`        应用名称

**选项**:
* `-h, --help`  显示帮助信息
* `--namespace [NAMESPACE], -n [NAMESPACE]` 应用的命名空间
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table

## everai app update             
通过manifest文件或者在`app.py`中创建App对象来更新一个应用。

**示例**:  

```bash
everai app update
```

**格式**: 
```bash
everai app update [-h] [--dry-run] [-f FROM_FILE]
```

**选项**:  

  * `-h, --help`            显示帮助信息
  * `--dry-run`
  * `-f FROM_FILE, --from-file FROM_FILE`
                        通过manifest文件（yaml格式）更新应用，示例：`--from-file <filename>`

## everai app pause               
停止应用，所有的worker都会停止  

**示例**:
```bash
everai worker list <your app name>
```

查看应用的worker列表，可以看到有2个正在运行的worker。  

```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
DFLH3kbnn9gyzzdk8Vypyx  RUNNING   FREE             2024-05-11 12:02:12+0800
KN63GutywVFnutrrQ6wLvw  RUNNING   FREE             2024-05-11 12:02:12+0800
```

执行如下的命令，停止示例中的应用。  

```bash
everai app pause <your app name>
```

再次查看应用的worker列表，可以看到之前在运行的2个worker已经被停止。  
 
```bash
everai worker list <your app name>

ID    STATUS    DETAIL_STATUS    CREATED_AT    DELETED_AT
----  --------  ---------------  ------------  ------------
```

执行`everai app list`，可以看到此时应用的状态是`PAUSED`。  

```bash
NAME          STATUS    CREATED_AT                ROUTE_NAME
------------  --------  ------------------------  ------------
test-start-4  PAUSED    2024-05-11 14:54:25+0800  test-start-4
```

**格式**:  
```bash
everai app pause [-h] [name]
```

**参数**:  

  * `name`        应用名称

**选项**:
  * `-h, --help`  显示帮助信息

## everai app resume           
恢复使用应用    

**示例**:

当你想恢复被停止（应用的状态为`PAUSED`）的应用时，可以执行如下命令：  

```bash
everai app resume <your app name>
```

**格式**:  
```bash
everai app resume [-h] [name]
```

**参数**:  

  * `name`        应用名称 

**选项**:  
* `-h, --help`            显示帮助信息  

## everai app prepare             
准备应用，所有被`@app.prepare`注解的方法都会被执行    

**示例**:  

 如果你的应用需要用到文件对象存储，那么在你的应用部署到云环境之前，你需要先创建一个卷。  

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
用例输出的结果如下所示：    
```bash
NAME       NAMESPACE    STATUS    WORKERS    CREATED_AT
---------  -----------  --------  ---------  ------------------------
get-start  default      DEPLOYED  3/3        2024-06-18T04:21:37+0000
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
用例输出的结果如下所示：  

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

## everai app event           
List events of app  

**示例**:  
```bash
everai app event <your app name>
```
用例输出的结果如下所示：

```bash
TYPE    FROM       CREATED_AT                MESSAGE
------  ---------  ------------------------  ------------------------------------------------------------------------------------------------
NORMAL  AppSever   2024-06-25T12:27:05+0000  Worker/oFHDnZHrDMo5WHnQkhcegx is ready now
NORMAL  AppSever   2024-06-25T12:27:05+0000  Worker/jQd38Z5HwRLv24x459CSBG is ready now
NORMAL  Scheduler  2024-06-25T12:26:59+0000  Successfully assigned worker/oFHDnZHrDMo5WHnQkhcegx to node/5a684c93-84c0-4078-821c-a4aeccb61407
NORMAL  Scheduler  2024-06-25T12:26:59+0000  Successfully assigned worker/jQd38Z5HwRLv24x459CSBG to node/5a684c93-84c0-4078-821c-a4aeccb61407
```

**格式**: 
```bash
everai app events [-h] [-n [NAMESPACE]] [--output [OUTPUT]] name
```

**参数**:  

 * `name`              应用名称

**选项**:  

 * `-h, --help`            显示帮助信息
 * `-n [NAMESPACE], --namespace [NAMESPACE]` 应用的命名空间
 * `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table

## everai app worker (workers, w)

管理应用的worker  

**格式**:
```bash 
everai app worker [-h] {list,ls} ...
```

**命令**:
  * `list (ls)`    应用的worker列表

**选项**:
  * `-h, --help`   显示帮助信息
  
### everai app worker (workers, w) list (ls)

应用的worker列表  

**示例**:
```bash
everai app worker list <your app name>
```

用例输出的结果如下所示：  
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
4gexdRcjZyAwPGNeNFNHpr  RUNNING   FREE             2024-06-25T12:19:05+0000
mHFmmq7fJWWtERAyFDLrcA  RUNNING   FREE             2024-06-25T12:19:05+0000
```

如果不输入应用名称，则会显示当前应用目录下该应用的worker列表。  
```bash
everai app worker list
```

用例输出的结果如下所示：  
```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
NwKKpZHesYBUncaw4bZdYz  RUNNING   FREE             2024-05-14 12:59:04+0800
```

**格式**:  
```bash 
everai app worker list [-h] [--output [OUTPUT]] [--all] [--recent-days [RECENT_DAYS]] [app_name]
```

**参数**:  
  * `app_name`              应用名称

**选项**:
  * `-h, --help`            显示帮助信息
  * `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide）
  * `--all, -a`         显示该应用的所有worker，包括已经删除的和有问题的worker
  * `--recent-days [RECENT_DAYS], -d [RECENT_DAYS]`
                         显示最近天数内没有在运行中的worker

## everai app check                 
检查应用目录下的代码编译是否通过  

**示例**:
```bash
everai app check
```
如果应用目录下的代码有语法错误，会出现如下的输出结果：  

```bash
find object in app.py: 'Service' object has no attribute 'rout'
```

**格式**: 
```bash 
everai app check [-h]
```

**选项**:  
* `-h, --help`       显示帮助信息  

## everai app delete                 
删除应用  

**示例**:
```bash
everai app delete <your app name>
```

**格式**: 
```bash 
everai app delete [-h] [--namespace [NAMESPACE]] [--force] name
```

**选项**:  
* `-h, --help`       显示帮助信息
* `--namespace [NAMESPACE], -n [NAMESPACE]` 应用的命名空间
* `--force`