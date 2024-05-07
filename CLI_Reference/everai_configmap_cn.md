# everai configmap
**格式**:  
```bash
everai configmap [-h] {create,delete,list,ls,get,update} ...
```

**命令**:  

* `create`              Create ConfigMap from file or literal string  
* `delete`              Delete configmap  
* `list (ls)`           List configmaps  
* `get`                 Get configmap  
* `update`              Update ConfigMap from file or literal string  

**选项**:  
* `-h, --help`            显示帮助信息  
 
## everai configmap create              
Create ConfigMap from file or literal string  

**示例**:  
```bash  
everai configmap create get-start-configmap --from-literal min_workers=1
```

**格式**: 
```bash 
everai configmap create [-h] [-l FROM_LITERAL] [-f FROM_FILE] name
```

**参数**:  
  * `name`                  The configmap name

**选项**:  
* `-h, --help`            显示帮助信息  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`
                        Create configmap from literal, for example: --from-literal name=user  
* `-f FROM_FILE, --from-file FROM_FILE`
                        Create configmap from file, for example: --from-file filename  

## everai configmap delete              
Delete configmap  

**示例**:  
```bash
everai configmap delete get-start-configmap
```
**格式**: 
```bash
everai configmap delete [-h] name
```

**参数**:  
  * `name`        The configmap name

**选项**:
* `-h, --help`  显示帮助信息

## everai configmap list (ls)           
List configmaps  

**示例**:  
```bash
everai configmap list
```
The result could be shown like this:  
```bash
NAME                   ITEMS
-------------------  -------
get-start-configmap        5
```

**格式**: 
```bash
everai configmap list [-h] [--output [OUTPUT]]
```
**选项**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide）  

## everai configmap get                 
Get configmap  

**示例**:  
```bash  
everai configmap get get-start-configmap
```

**格式**:  
```bash
everai configmap get [-h] [--output [OUTPUT]] name  
```

**参数**:  
  * `name`                  The configmap name

**选项**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide）

## everai configmap update              
Update ConfigMap from file or literal string  

**示例**:  
```bash  
everai configmap update --from-literal min_workers=1 \
  --from-literal max_workers=5 \
  --from-literal max_queue_size=2 \
  --from-literal scale_up_step=1 \
  --from-literal max_idle_time=60 get-start-configmap
```

**格式**: 
```bash
everai configmap update [-h] [-l FROM_LITERAL] [-f FROM_FILE] name
```

**参数**:  
  * `name`                  The configmap name

**选项**:  
* `-h, --help`            显示帮助信息  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`  
                        Update configmap from literal, for example: --from-literal name=user
`-f FROM_FILE, --from-file FROM_FILE`  
                        Update configmap from file, for example: --from-file filename