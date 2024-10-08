# everai worker
管理应用的worker  

**格式**:   
```bash 
everai worker [-h] {list,ls,exec} ...
```

**命令**:    
* `list (ls)` 显示属于该应用的worker列表  
* `exec`          在运行中的worker容器中执行命令

**选项**:  
* `-h, --help`  显示帮助信息  

## everai worker list (ls)
应用的worker列表  

**示例**:  
```bash 
everai worker list <your app name>
```
用例输出的结果如下所示，`CREATED_AT`和`DELETED_AT`使用UTC时间显示。  
```bash 
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
4gexdRcjZyAwPGNeNFNHpr  RUNNING   FREE             2024-06-25T12:19:05+0000
mHFmmq7fJWWtERAyFDLrcA  RUNNING   FREE             2024-06-25T12:19:05+0000
```
如果不输入应用名称，在当前应用目录下命令行工具会根据`app.py`中的应用名称显示worker列表。  

```bash
everai worker list
```
用例输出的结果如下所示：  

```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
8UtmxyvQ4qC67awQMKDgJb  RUNNING   FREE             2024-06-26T08:54:23+0000
```
**格式**:  
```bash  
everai worker list [-h] [--output [OUTPUT]] [--all] [--recent-days [RECENT_DAYS]] [--namespace [NAMESPACE]] [app_name]
```

**参数**:  
  * `app_name`              应用名称  

**选项**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                         输出格式，可选（json, yaml, table, wide），默认为table  
* `--all, -a`             显示该应用的所有worker，包括已经删除的和有问题的worker  
* `--recent-days [RECENT_DAYS], -d [RECENT_DAYS]`
                        显示该应用最近天数内的所有worker，包括已经删除的和有问题的worker
* `--namespace [NAMESPACE], -n [NAMESPACE]`
                        应用的命名空间  

## everai worker exec
在运行中的worker容器中执行命令  

**示例**:  

当你想访问worker容器的bash shell环境，可以执行如下命令：  

```bash 
everai worker exec -it dweBRSPD395BvtBDsZYum8 bash
```

命令执行后，当前的终端环境已经进入到worker容器内`workspace`目录下。  

```bash 
root@b0b6f096aeab:/workspace#
```

**格式**:  
```bash  
everai worker exec [-h] [--interactive] [--tty] worker command ...
```

**参数**:  
  * `worker`             worker id
  * `command`            命令
  * `args`               worker容器中的命令参数

**选项**:  
* `-h, --help`            显示帮助信息  
* `--interactive, -i`  以交互模式执行命令，允许输入和输出
* `--tty, -t`          为worker容器分配一个伪终端或终端