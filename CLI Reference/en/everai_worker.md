# everai worker
Manage the worker of app  

**Usage**:   
```bash 
everai worker [-h] {list,ls,exec} ...
```

**Commands**:    
* `list (ls)` List workers of app  
* `exec`          Run a command in a running worker

**Options**:  
* `-h, --help`  show this help message and exit  

## everai worker list (ls)
List workers of app  

**Example**:  
```bash 
everai worker list <your app name>
```
The result could be shown like this, and `CREATED_AT` and `DELETED_AT` use UTC time display.  
```bash 
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
4gexdRcjZyAwPGNeNFNHpr  RUNNING   FREE             2024-06-25T12:19:05+0000
mHFmmq7fJWWtERAyFDLrcA  RUNNING   FREE             2024-06-25T12:19:05+0000
```

If the app name is not attached, everai command line tool finds app name in `app.py` which is in current app directory.  

```bash
everai worker list
```
The output for this example is this:  

```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
8UtmxyvQ4qC67awQMKDgJb  RUNNING   FREE             2024-06-26T08:54:23+0000
```

**Usage**:  
```bash  
everai worker list [-h] [--output [OUTPUT]] [--all] [--recent-days [RECENT_DAYS]] [--namespace [NAMESPACE]] [app_name]
```

**Positional arguments**:  
  * `app_name`              The app name  

**Options**:  
* `-h, --help`            show this help message and exit  
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)  
* `--all, -a`             show all workers, include deleted and errors  
* `--recent-days [RECENT_DAYS], -d [RECENT_DAYS]`
                        show all workers created in recent days, include deleted and errors
* `--namespace [NAMESPACE], -n [NAMESPACE]`
                        namespace of app  

## everai worker exec
Run a command in a running worker  

**Example**:  

when you want to access the bash shell environment of the worker container, you can execute the following command:  

```bash 
everai worker exec -it dweBRSPD395BvtBDsZYum8 bash
```

After the command is executed, the current terminal environment has entered the `workspace` directory in the worker container.  

```bash 
root@b0b6f096aeab:/workspace#
```

**Usage**:  
```bash  
everai worker exec [-h] [--interactive] [--tty] worker command ...
```

**Positional arguments**:  
  * `worker`             worker id
  * `command`            command
  * `args`               arguments for command in worker

**Options**:  
* `-h, --help`            show this help message and exit  
* `--interactive, -i`  Keep STDIN open even if not attached
* `--tty, -t`          Allocate a pseudo-TTY, just for compatible client, no effect