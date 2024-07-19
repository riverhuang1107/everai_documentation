# everai volume
管理卷  

**格式**:  
```bash  
everai volume [-h] {create,list,ls,delete,get,pull,push} ...
```

**命令**:  
* `create`              创建一个卷  
* `list (ls)`           显示卷列表  
* `delete`              删除一个卷  
* `get`                 显示一个卷的详细信息  
* `pull`                从[EverAI](https://everai.expvent.com)平台拉取一个卷到本地  
* `push`                推送一个卷到[EverAI](https://everai.expvent.com)平台  
* `publish`              把你的指定卷公开给所有人

**选项**:  
* `-h, --help`            显示帮助信息

## everai volume create              
创建一个卷  

**示例**:  
```bash
everai volume create test-volume
```

**格式**:  
```bash
everai volume create [-h] name
```

**参数**:  

  * `name`        卷名称

**选项**:  

 * `-h, --help`  显示帮助信息

## everai volume list (ls)           
显示卷列表  

**示例**:
```bash
everai volume list
```
用例输出的结果如下所示，`CREATED_AT`使用UTC时间显示。  

```bash
NAME               REVISION    CREATED_AT                  FILES  SIZE    STATUS
-----------------  ----------  ------------------------  -------  ------  --------
demo-volume        000000-000  2024-05-10 14:10:25+0800        0  0 B     Public
get-start-volume   000001-08a  2024-05-10 11:57:41+0800        1  11 B    Private
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
删除一个卷    

**示例**:  
```bash 
everai volume delete get-start-volume --all
```

**格式**:   
```bash 
everai volume delete [-h] [--local] [--cloud] [--all] name
```

**参数**:
  * `name`        卷名称

**选项**:
  * `-h, --help`  显示帮助信息  
  * `--local`     只删除本地的卷  
  * `--cloud`     只删除平台云端的卷  
  * `--all`       同时删除本地和平台云端的卷  

## everai volume get                 
显示一个卷的详细信息  

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
  * `name`        卷名称

**选项**:    
* `-h, --help`  显示帮助信息

## everai volume pull                
从[EverAI](https://everai.expvent.com)平台拉取一个卷到本地

**示例**:  
```bash
everai volume pull get-start-volume
```
**格式**:  
```bash   
everai volume pull [-h] [--force] [--sync] name
```

**参数**:  
  * `name`        卷名称  

**选项**:  
* `-h, --help`  显示帮助信息  
* `--force`     强制从远端拉取卷文件到本地，系统不会比对`metadata`文件版本。如果不加`--force`，如果你本地的`metadata`文件版本和远端的一致，拉取会停止。    
* `--sync`      从远端同步文件，如果你的文件在本地存在，但在远端已经不存在，同步后这个本地文件会被删除。注意：需要和`--force`一起使用, `--sync`才会生效。    

## everai volume push                
推送一个卷到[EverAI](https://everai.expvent.com)平台  

**示例**:  
```bash
everai volume push get-start-volume
```

**格式**:
```bash   
everai volume push [-h] name
```
**参数**:  
  * `name`        卷名称  

**选项**:  
* `-h, --help`  显示帮助信息

## everai volume publish
 
把你的指定卷公开给所有人  

**示例**:  
```bash   
 everai volume publish demo-volume
```  

公开卷后，执行`everai volume list`后，可以看到，卷的状态变为`Public`。`CREATED_AT`使用UTC时间显示。  
  
```bash   
NAME               REVISION    CREATED_AT                  FILES  SIZE    STATUS
-----------------  ----------  ------------------------  -------  ------  --------
demo-volume        000000-000  2024-05-10 14:10:25+0800        0  0 B     Public
```
**格式**:
```bash 
ever volume publish [-h] name
```
**参数**:  

* `name`        卷名称

**选项**:  

* `-h, --help`  显示帮助信息

