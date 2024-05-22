# everai app
应用管理  

**格式**:   
```bash
everai app [-h] {create,run,get,upgrade,pause,deploy,prepare,list,ls,queue,q,worker,workers,w} ...
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
  * `worker (workers, w)` 管理应用的worker 

**选项**:  
  * `-h, --help`            显示帮助信息

## everai app create             
创建一个应用  

**示例**:
```bash
everai app create <your app name>
```
执行`everai app create`命令时，如果出现`Route path exist`报错提示信息，你可以通过指定一个新的路由名称来解决该问题。  

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
                        路由名称全局唯一。一般来说，路由名称与应用名称相同。如果你指定的应用名称系统中已存在，你需要指定一个新的路由名称。

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

当你需要更新你应用中的代码时，你需要更新你的应用镜像。详细介绍请参考[everai app upgrade --image](https://expvent.com.cn:1111/everai-documentation-web/zh-cn/docs/Guide/App_Upgrade#everai-app-upgrade---image)。  
```bash
everai app upgrade --image
```

**格式**: 
```bash
everai app upgrade [-h] [--autoscaling-policy] [--resource-requests] [--volume-requests] [--secret-requests] [--image] [--all]
```

**选项**:  

  * `-h, --help`            显示帮助信息
  * `--autoscaling-policy`  只更新自动扩缩容策略
  * `--resource-requests`   只更新计算资源，这个选项会引起worker重新部署
  * `--volume-requests`     只更新卷，这个选项会引起worker重新部署
  * `--secret-requests`     只更新密钥，这个选项会引起worker重新部署
  * `--image`               只更新镜像，这个选项会引起worker重新部署
  * `--all`                 更新所有设置，这个选项会引起worker重新部署

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

## everai app deploy              
部署应用到可用状态  

**示例**:  
```bash
 everai app deploy
```
执行`everai app list`后，可以看到类似如下的输出结果。如果你的应用状态是`DEPLOYED`，意味着你的应用已经部署成功。   
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

## everai app worker (workers, w)

管理应用的worker  

**Usage**:
```bash 
everai app worker [-h] {list,ls} ...
```

**Commands**:
  * `list (ls)`    应用的worker列表

**Options**:
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
UskkMtU5HihJhiyWevc8We  RUNNING   FREE             2024-05-14 09:47:54+0800
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
  * `--recent-days` [RECENT_DAYS], -d [RECENT_DAYS]
                         显示最近天数内没有在运行中的worker

                        

