# everai volume
**格式**:  
```bash  
everai volume [-h] {create,list,ls,delete,get,pull,push} ...
```

**命令**:  
* `create`              Create volume  
* `list (ls)`           List volume  
* `delete`              Delete volume  
* `get`                 Get volume  
* `pull`                Pull volume  
* `push`                Push volume  

**选项**:  
* `-h, --help`            show this help message and exit

## everai volume create              
Create volume  
## everai volume list (ls)           
List volume  

**示例**:
```bash
everai volume list
```
The result could be shown like this:  

```bash
NAME              REVISION    CREATED_AT                  FILES  SIZE
----------------  ----------  ------------------------  -------  ------
get-start-volume  000001-084  2024-04-29 15:41:34+0800        1  11 B
```

**格式**:
```bash 
everai volume list [-h] [--output [OUTPUT]]
```

**选项**:  
* `-h, --help`            show this help message and exit  
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide）  

## everai volume delete              
Delete volume  

**示例**:  
```bash 
everai volume delete get-start-volume --all
```

**格式**:   
```bash 
everai volume delete [-h] [--local] [--cloud] [--all] name
```

**Positional arguments**:
  * `name`        The volume name

**选项**:
  * `-h, --help`  show this help message and exit  
  * `--local`     Delete the local cache of volume only  
  * `--cloud`     Delete the volume in cloud, and reserve local cahce  
  * `--all`       Delete volume both cache and in-cloud  

## everai volume get                 
Get volume  

**示例**:  
```bash
everai volume get get-start-volume
```
The result could be shown like this:  
```bash
<Volume: id: hupa49MesPXwmhFSy9Ku44, name: get-start-volume, revision: 000001-b9c, files: 1, size: 11 B>
path: /home/<username>/.cache/everai/volumes/hupa49MesPXwmhFSy9Ku44
```
**格式**:  
```bash
everai volume get [-h] name
```

**参数**:  
  * `name`        The volume name

**选项**:    
* `-h, --help`  show this help message and exit

## everai volume pull                
Pull volume  

**示例**:  
```bash
everai volume pull get-start-volume
```
**格式**:  
```bash   
everai volume pull [-h] [--force] [--sync] name
```

**参数**:  
  * `name`        The volume name  

**选项**:  
* `-h, --help`  show this help message and exit  
* `--force`     Force pull remote volume file to local, if your volume local metadata revision equal remote volume
              revision, pull will stop, if add `--force`, pull will ignore revision compare  
* `--sync`      Sync file form remote, if this file local have, but remote not exist, then this local file will be
              delete. notice: only use argument `--force`, `--sync` will come into effect  

## everai volume push                
Push volume  

**示例**:  
```bash
everai volume push get-start-volume
```

**格式**:
```bash   
everai volume push [-h] name
```
**参数**:  
  * `name`        The volume name  

**选项**:  
* `-h, --help`  show this help message and exit

