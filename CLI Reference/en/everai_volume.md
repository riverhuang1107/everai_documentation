# everai volume
Manage volume  

**Usage**:  
```bash  
everai volume [-h] {create,list,ls,delete,get,pull,push} ...
```

**Commands**:  
* `create`              Create volume  
* `list (ls)`           List volume  
* `delete`              Delete volume  
* `get`                 Get volume  
* `pull`                Pull volume  
* `push`                Push volume  
* `publish`             Publish volume to everyone

**Options**:  
* `-h, --help`            show this help message and exit

## everai volume create              
Create volume  

**Example**:  
```bash
everai volume create test-volume
```

**Usage**:  
```bash
everai volume create [-h] name
```

**Positional arguments**:
  * `name`        The volume name 

**Options**:
  * `-h, --help`  show this help message and exit 

## everai volume list (ls)           
List volume  

**Example**:
```bash
everai volume list
```
The result could be shown like this:  

```bash
NAME               REVISION    CREATED_AT                  FILES  SIZE    STATUS
-----------------  ----------  ------------------------  -------  ------  --------
demo-volume        000000-000  2024-05-10 14:10:25+0800        0  0 B     Public
get-start-volume   000001-08a  2024-05-10 11:57:41+0800        1  11 B    Private
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

**Example**:  
```bash 
everai volume delete get-start-volume --all
```

**Usage**:   
```bash 
everai volume delete [-h] [--local] [--cloud] [--all] name
```

**Positional arguments**:
  * `name`        The volume name

**Options**:
  * `-h, --help`  show this help message and exit  
  * `--local`     Delete the local cache of volume only  
  * `--cloud`     Delete the volume in cloud, and reserve local cache  
  * `--all`       Delete volume both cache and in-cloud  

## everai volume get                 
Get volume  

**Example**:  
```bash
everai volume get get-start-volume
```
The result could be shown like this:  
```bash
<Volume: id: hupa49MesPXwmhFSy9Ku44, name: get-start-volume, revision: 000001-b9c, files: 1, size: 11 B>
path: /home/<username>/.cache/everai/volumes/hupa49MesPXwmhFSy9Ku44
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

**Example**:  
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
              delete. notice: only use argument `--force`, `--sync` will come into effect  

## everai volume push                
Push volume  

**Example**:  
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

## everai volume publish
Publish volume to everyone  

**Example**:  
```bash   
 everai volume publish demo-volume
```

After publishing the volume, run command `everai volume list`, the status is changed from `Private` to `Public`.  
  
```bash   
NAME               REVISION    CREATED_AT                  FILES  SIZE    STATUS
-----------------  ----------  ------------------------  -------  ------  --------
demo-volume        000000-000  2024-05-10 14:10:25+0800        0  0 B     Public
```
**Usage**:
```bash 
ever volume publish [-h] name
```
**Positional arguments**:  

* `name`        The volume name

**Options**:  

* `-h, --help`  show this help message and exit

