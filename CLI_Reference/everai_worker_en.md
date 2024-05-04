# everai worker
**Usage**:   
```bash 
everai worker [-h] {list,ls} ...  
```

**Positional arguments**:    
  {list,ls}   Worker command help  
* `list (ls)`
              List workers of app  

**Options**:  
* `-h, --help`  show this help message and exit  

## everai worker list (ls)
List workers of app  

**Examples**:  
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

**Usage**:  
```bash  
everai worker list [-h] [--output [OUTPUT]] [--all] [--recent-days [RECENT_DAYS]] app_name
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