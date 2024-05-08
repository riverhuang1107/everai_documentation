# everai configmap
**格式**:  
```bash
everai configmap [-h] {create,delete,list,ls,get,update} ...
```

**命令**:  

* `create`              通过文件或者字符串创建ConfigMap  
* `delete`              删除ConfigMap  
* `list (ls)`           显示ConfigMap列表  
* `get`                 显示一个ConfigMap的详细信息  
* `update`              通过文件或者字符串更新ConfigMap  

**选项**:  
* `-h, --help`            显示帮助信息  
 
## everai configmap create              
通过文件或者字符串创建ConfigMap  

**示例**:  
```bash  
everai configmap create get-start-configmap --from-literal min_workers=1
```

**格式**: 
```bash 
everai configmap create [-h] [-l FROM_LITERAL] [-f FROM_FILE] name
```

**参数**:  
  * `name`                  ConfigMap名称

**选项**:  
* `-h, --help`            显示帮助信息  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`
                        从字符串创建ConfigMap，举例：`--from-literal name=user`  
* `-f FROM_FILE, --from-file FROM_FILE`
                        通过文件创建ConfigMap，举例：`--from-file filename`  

## everai configmap delete              
删除ConfigMap  

**示例**:  
```bash
everai configmap delete get-start-configmap
```
**格式**: 
```bash
everai configmap delete [-h] name
```

**参数**:  
  * `name`        ConfigMap名称

**选项**:
* `-h, --help`  显示帮助信息

## everai configmap list (ls)           
显示ConfigMap列表  

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
                        输出格式，可选（json, yaml, table, wide），默认为table  

## everai configmap get                 
显示一个ConfigMap的详细信息  

**示例**:  
```bash  
everai configmap get get-start-configmap
```

**格式**:  
```bash
everai configmap get [-h] [--output [OUTPUT]] name  
```

**参数**:  
  * `name`                  ConfigMap名称

**选项**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table

## everai configmap update              
通过文件或者字符串更新ConfigMap  

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
  * `name`                  ConfigMap名称

**选项**:  
* `-h, --help`            显示帮助信息  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`  通过字符串更新ConfigMap，举例：`--from-literal name=user`  
* `-f FROM_FILE, --from-file FROM_FILE`  通过文件更新ConfigMap，举例：`--from-file filename`  
