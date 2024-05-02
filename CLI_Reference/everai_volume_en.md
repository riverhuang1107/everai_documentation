# everai volume
**Usage**:  
```bash  
everai volume [-h] {create,list,ls,delete,get,pull,push} ...
```

**Positional arguments**:  
  {create,list,ls,delete,get,pull,push}
                        Volume command help  
* `create`              Create volume  
* `list (ls)`           List volume  
* `delete`              Delete volume  
* `get`                 Get volume  
* `pull`                Pull volume  
* `push`                Push volume  

**Options**:  
* `-h, --help`            show this help message and exit

## everai volume create              
Create volume  
## everai volume list (ls)           
List volume  
**Examples**:
```bash
everai volume list
```

**Usage**:
```bash 
everai volume list [-h] [--output [OUTPUT]]
```

**Options**:  
* `-h, --help`            show this help message and exit  
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)  

## everai volume delete              
Delete volume
## everai volume get                 
Get volume  
**Examples**:  
```bash
everai volume get get-start-volume
```

**Usage**:  
```bash
everai volume get [-h] name
```

**Positional arguments**:  
  * `name`        The volume name

**Options**:    
* `-h, --help`  show this help message and exit

## everai volume pull                
Pull volume  
**Examples**:  
```bash
everai volume pull get-start-volume
```
**Usage**:  
```bash   
everai volume pull [-h] [--force] [--sync] name
```

**Positional arguments**:  
  * `name`        The volume name  

**Options**:  
* `-h, --help`  show this help message and exit  
* `--force`     Force pull remote volume file to local, if your volume local metadata revision equal remote volume
              revision, pull will stop, if add `--force`, pull will ignore revision compare  
* `--sync`      Sync file form remote, if this file local have, but remote not exist, then this local file will be
              delete. notice: only use argument `--force`, --sync will come into effect  

## everai volume push                
Push volume
**Examples**:  
```bash
everai volume push get-start-volume
```

**Usage**:
```bash   
everai volume push [-h] name
```
**Positional arguments**:  
  * `name`        The volume name  

**Options**:  
* `-h, --help`  show this help message and exit

