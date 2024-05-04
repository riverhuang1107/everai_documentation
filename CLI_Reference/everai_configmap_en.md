# everai configmap
**Usage**:  
```bash
everai configmap [-h] {create,delete,list,ls,get,update} ...
```

**Positional arguments**:  
  {create,delete,list,ls,get,update}
                        Configmap command help  
* `create`              Create ConfigMap from file or literal string  
* `delete`              Delete configmap  
* `list (ls)`           List configmaps  
* `get`                 Get configmap  
* `update`              Update ConfigMap from file or literal string  

**Options**:  
* `-h, --help`            show this help message and exit  
 
## everai configmap create              
Create ConfigMap from file or literal string  

**Examples**:  
```bash  
everai configmap create get-start-configmap --from-literal min_workers=1
```

**Usage**: 
```bash 
everai configmap create [-h] [-l FROM_LITERAL] [-f FROM_FILE] name
```

**Positional arguments**:  
  * `name`                  The configmap name

**Options**:  
* `-h, --help`            show this help message and exit  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`
                        Create configmap from literal, for example: --from-literal name=user  
* `-f FROM_FILE, --from-file FROM_FILE`
                        Create configmap from file, for example: --from-file filename  

## everai configmap delete              
Delete configmap  

**Examples**:  
```bash
everai configmap delete get-start-configmap
```
**Usage**: 
```bash
everai configmap delete [-h] name
```

**Positional arguments**:  
  * `name`        The configmap name

**Options**:
* `-h, --help`  show this help message and exit

## everai configmap list (ls)           
List configmaps  

**Examples**:  
```bash
everai configmap list
```
The result could be shown like this:  
```bash
NAME                   ITEMS
-------------------  -------
get-start-configmap        5
```

**Usage**: 
```bash
everai configmap list [-h] [--output [OUTPUT]]
```
**Options**:  
* `-h, --help`            show this help message and exit  
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)  

## everai configmap get                 
Get configmap  

**Examples**:  
```bash  
everai configmap get get-start-configmap
```

**Usage**:  
```bash
everai configmap get [-h] [--output [OUTPUT]] name  
```

**Positional arguments**:  
  * `name`                  The configmap name

**Options**:  
* `-h, --help`            show this help message and exit  
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)

## everai configmap update              
Update ConfigMap from file or literal string  

**Examples**:  
```bash  
everai configmap update --from-literal min_workers=1 \
  --from-literal max_workers=5 \
  --from-literal max_queue_size=2 \
  --from-literal scale_up_step=1 \
  --from-literal max_idle_time=60 get-start-configmap
```

**Usage**: 
```bash
everai configmap update [-h] [-l FROM_LITERAL] [-f FROM_FILE] name
```

**Positional arguments**:  
  * `name`                  The configmap name

**Options**:  
* `-h, --help`            show this help message and exit  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`  
                        Update configmap from literal, for example: --from-literal name=user
`-f FROM_FILE, --from-file FROM_FILE`  
                        Update configmap from file, for example: --from-file filename