# everai configmap
ConfigMap管理  

**格式**:  
```bash
everai configmap [-h] {create,delete,list,ls,get,update} ...
```

**命令**:  

* `create`              通过配置文件或者字符串键值对创建ConfigMap  
* `delete`              删除ConfigMap  
* `list (ls)`           显示ConfigMap列表  
* `get`                 显示一个ConfigMap的详细信息  
* `update`              通过配置文件或者字符串键值对更新ConfigMap  

**选项**:  
* `-h, --help`            显示帮助信息  
 
## everai configmap create              
通过配置文件或者字符串键值对创建ConfigMap  

**示例**:  

通过命令行设置字符串键值对的方式创建一个ConfigMap。  

```bash  
everai configmap create test-configmap \ 
  --from-literal min_workers=1 \
  --from-literal max_workers=5 \
  --from-literal max_queue_size=2 \
  --from-literal scale_up_step=1 \
  --from-literal max_idle_time=60
```

通过一个`yaml`配置文件创建一个ConfigMap。  

首先创建一个名为test-configmap的`yaml`文件，文件内容如下所示：  

```bash
name: test-configmap
data:
  max_idle_time: '60'
  max_queue_size: '2'
  max_workers: '5'
  min_workers: '1'
  scale_up_step: '1'
```
然后，运行如下所示命令创建ConfigMap。  

```bash  
everai configmap create --from-file test-configmap.yml test-configmap
```

**格式**: 
```bash 
everai configmap create [-h] [-f FROM_FILE] [-l FROM_LITERAL] [name]
```

**参数**:  
  * `name`                  ConfigMap名称

**选项**:  
* `-h, --help`            显示帮助信息  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`
                        通过命令行设置字符串键值对的方式创建一个ConfigMap，举例：`--from-literal name=user`  
* `-f FROM_FILE, --from-file FROM_FILE`
                        通过配置文件的方式创建ConfigMap，举例：`--from-file filename`  

## everai configmap delete              
删除ConfigMap  

**示例**:  
```bash
everai configmap delete test-configmap
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
用例输出的结果如下所示：    
```bash
NAME                    ITEMS
--------------------  -------
get-start-configmap         5
test-configmap              5
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
everai configmap get test-configmap
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
通过配置文件或者字符串键值对更新ConfigMap  

**示例**:  

通过命令行设置字符串键值对的方式更新一个ConfigMap。

```bash  
everai configmap update --from-literal min_workers=2 \
  --from-literal max_workers=5 \
  --from-literal max_queue_size=2 \
  --from-literal scale_up_step=1 \
  --from-literal max_idle_time=60 test-configmap
```

通过`yaml`配置文件更新ConfigMap。

首先创建一个名为test-configmap的`yaml`文件，文件内容如下所示：  

```bash
name: test-configmap
data:
  max_idle_time: '60'
  max_queue_size: '2'
  max_workers: '5'
  min_workers: '2'
  scale_up_step: '1'
```

然后，运行如下所示命令更新ConfigMap。  

```bash  
everai configmap update --from-file test-configmap.yml test-configmap
```
`everai configmap update`执行后，使用该ConfigMap的应用都会更新相关策略。举例来说，把`min_workers`从1更新为2，系统会把相关涉及到的应用的最小运行worker数量都扩容到2。

**格式**: 
```bash
everai configmap update [-h] [-f FROM_FILE] [-l FROM_LITERAL] [name]
```

**参数**:  
  * `name`                  ConfigMap名称

**选项**:  
* `-h, --help`            显示帮助信息  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`  通过命令行设置字符串键值对的方式更新ConfigMap，举例：`--from-literal name=user`  
* `-f FROM_FILE, --from-file FROM_FILE`  通过配置文件更新ConfigMap，举例：`--from-file filename`  
