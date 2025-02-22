# everai volume
管理数据卷  

**格式**:  
```bash  
everai volume [-h] {create,list,ls,delete,get,pull,push,publish,tree,tr} ...
```

**命令**:  
* `create`              创建一个数据卷  
* `list (ls)`           显示数据卷列表  
* `delete`              删除一个数据卷  
* `get`                 显示一个数据卷的详细信息  
* `pull`                从[EverAI](https://everai.expvent.com)平台拉取一个数据卷到本地  
* `push`                推送一个数据卷到[EverAI](https://everai.expvent.com)平台  
* `publish`             把你的指定数据卷公开给所有人
* `tree (tr)`           显示数据卷的目录结构

**选项**:  
* `-h, --help`            显示帮助信息

## everai volume create              
创建一个数据卷  

**示例**:  
```bash
everai volume create test-volume
```

**格式**:  
```bash
everai volume create [-h] name
```

**参数**:  

  * `name`        数据卷名称

**选项**:  

 * `-h, --help`  显示帮助信息

## everai volume list (ls)           
显示数据卷列表  

**示例**:
```bash
everai volume list
```
用例输出的结果如下所示，`CREATED_AT`使用UTC时间显示。  

```bash
NAME                                     REVISION    CREATED_AT                  FILES  SIZE          STATUS
---------------------------------------  ----------  ------------------------  -------  ------------  --------
get-start-volume                         000008-5d6  2024-05-28T12:46:09+0000        1  42 B          Private
test-start-volume                        000003-490  2024-06-24T02:04:19+0000        1  100 B         Private
```

**格式**:
```bash 
everai volume list [-h] [--output [OUTPUT]]
```

**选项**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table  

## everai volume delete              
删除一个数据卷    

**示例**:  
```bash 
everai volume delete get-start-volume --all
```

**格式**:   
```bash 
everai volume delete [-h] [--local] [--cloud] [--all] name
```

**参数**:
  * `name`        数据卷名称

**选项**:
  * `-h, --help`  显示帮助信息  
  * `--local`     只删除本地的数据卷  
  * `--cloud`     只删除平台云端的数据卷  
  * `--all`       同时删除本地和平台云端的数据卷  

## everai volume get                 
显示一个数据卷的详细信息  

**示例**:  
```bash
everai volume get get-start-volume
```
用例输出的结果如下所示：  

```bash
<Volume: id: hupa49MesPXwmhFSy9Ku44, name: get-start-volume, revision: 000001-b9c, files: 1, size: 11 B>
path: /home/<username>/.cache/everai/volumes/hupa49MesPXwmhFSy9Ku44
```
**格式**:  
```bash
everai volume get [-h] name
```

**参数**:  
  * `name`        数据卷名称

**选项**:    
* `-h, --help`  显示帮助信息

## everai volume pull                
从[EverAI](https://everai.expvent.com)平台拉取一个数据卷到本地

**示例**:  
```bash
everai volume pull get-start-volume
```
**格式**:  
```bash   
everai volume pull [-h] [--force] [--sync] name
```

**参数**:  
  * `name`        数据卷名称  

**选项**:  
* `-h, --help`  显示帮助信息  
* `--force`     强制从远端拉取数据卷文件到本地，系统不会比对`metadata`文件版本。如果不加`--force`，如果你本地的`metadata`文件版本和远端的一致，拉取会停止。    
* `--sync`      从远端同步文件，如果你的文件在本地存在，但在远端已经不存在，同步后这个本地文件会被删除。注意：需要和`--force`一起使用, `--sync`才会生效。    

## everai volume push                
推送一个数据卷到[EverAI](https://everai.expvent.com)平台  

**示例**:  
```bash
everai volume push get-start-volume
```

终端显示文件推送进度，用例输出的结果如下所示：  

```bash
Uploading ...ots/462165984030d82259a11f4367a4eed129e94a7b/model_index.json: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 609/609 [00:00<00:00, 1799.51B/s]
Uploading ...984030d82259a11f4367a4eed129e94a7b/text_encoder_2/config.json: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 575/575 [00:00<00:00, 2007.34B/s]
Uploading                                            files:   5%|███████▊                                                                                                                                        | 2/37 [00:01<00:21,  1.64file/s]
Uploading ...9a11f4367a4eed129e94a7b/text_encoder_2/model.fp16.safetensors:   0%|                                                                                                                                    | 0/1325.01 [00:00<?, ?MiB/s]
```

**格式**:
```bash   
everai volume push [-h] [-u] name
```
**参数**:  
  * `name`        数据卷名称  

**选项**:  
* `-h, --help`  显示帮助信息
* `-u, --update`  针对本地源文件存在且云端目标文件不存在，或本地源文件的大小与云端目标文件不一致的情况，进行数据卷推送操作。

## everai volume publish
 
把你的指定数据卷公开给所有人  

**示例**:  
```bash   
 everai volume publish demo-volume
```  

公开数据卷后，执行`everai volume list`后，可以看到，数据卷的状态变为`Public`。`CREATED_AT`使用UTC时间显示。  

```bash   
NAME               REVISION    CREATED_AT                  FILES  SIZE    STATUS
-----------------  ----------  ------------------------  -------  ------  --------
demo-volume        000000-000  2024-05-10T06:10:25+0000        0  0 B     Public
```
**格式**:
```bash 
ever volume publish [-h] name
```
**参数**:  

* `name`        数据卷名称

**选项**:  

* `-h, --help`  显示帮助信息

## everai volume tree
 
显示数据卷的目录结构  

**示例**:  
```bash   
everai volume tree get-start-volume
```  

用例输出的结果如下所示：  

```bash   
get-start-volume
└── my-model
```
**格式**:
```bash 
everai volume tree [-h] name
```
**参数**:  

* `name`        数据卷名称

**选项**:  

* `-h, --help`  显示帮助信息


