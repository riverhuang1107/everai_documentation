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
everai worker list get-start
```

```bash
everai workers list
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