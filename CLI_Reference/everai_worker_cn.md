# everai worker
**格式**:   
```bash 
everai worker [-h] {list,ls} ...  
```

**命令**:    
* `list (ls)`
              List workers of app  

**选项**:  
* `-h, --help`  显示帮助信息  

## everai worker list (ls)
List workers of app  

**示例**:  
```bash 
everai worker list <your app name>
```
The result could be shown like this:  
```bash 
ID                      STATUS                               DETAIL_STATUS                                CREATED_AT                DELETED_AT
----------------------  -----------------------------------  -------------------------------------------  ------------------------  ------------
RF7crg44K8QoYSzvKQCkhh  V1WorkerWorkerStatus.STATUS_RUNNING  WorkerWorkerDetailStatus.DETAIL_STATUS_FREE  2024-04-29 20:53:40+0800
```
```bash
everai worker list
```

**格式**:  
```bash  
everai worker list [-h] [--output [OUTPUT]] [--all] [--recent-days [RECENT_DAYS]] app_name
```

**参数**:  
  * `app_name`              The app name  

**选项**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)  
* `--all, -a`             show all workers, include deleted and errors  
* `--recent-days [RECENT_DAYS], -d [RECENT_DAYS]`
                        show not running workers who is created in recent days  