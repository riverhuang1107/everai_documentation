# everai worker
**格式**:   
```bash 
everai worker [-h] {list,ls} ...  
```

**命令**:    
* `list (ls)` 显示属于该应用的worker列表  

**选项**:  
* `-h, --help`  显示帮助信息  

## everai worker list (ls)
应用的worker列表  

**示例**:  
```bash 
everai worker list <your app name>
```
用例输出的结果如下所示：  
```bash 
ID                      STATUS                               DETAIL_STATUS                                CREATED_AT                DELETED_AT
----------------------  -----------------------------------  -------------------------------------------  ------------------------  ------------
RF7crg44K8QoYSzvKQCkhh  V1WorkerWorkerStatus.STATUS_RUNNING  WorkerWorkerDetailStatus.DETAIL_STATUS_FREE  2024-04-29 20:53:40+0800
```
如果不输入应用名称，则会显示当前应用目录下该应用的worker列表。  

```bash
everai worker list
```
用例输出的结果如下所示：  

```bash
ID                      STATUS          DETAIL_STATUS       CREATED_AT                DELETED_AT
----------------------  --------------  ------------------  ------------------------  ------------
KFZtizCb6eR3E2RTvivzmP  STATUS_RUNNING  DETAIL_STATUS_FREE  2024-05-07 12:36:29+0800
```
**格式**:  
```bash  
everai worker list [-h] [--output [OUTPUT]] [--all] [--recent-days [RECENT_DAYS]] app_name
```

**参数**:  
  * `app_name`              应用名称  

**选项**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                         输出格式，可选（json, yaml, table, wide），默认为table  
* `--all, -a`             显示该应用的所有worker，包括已经删除的和有问题的worker  
* `--recent-days [RECENT_DAYS], -d [RECENT_DAYS]`
                        显示最近天数内没有在运行中的worker  