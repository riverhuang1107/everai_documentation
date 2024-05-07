# everai volume
**格式**:  
```bash  
everai volume [-h] {create,list,ls,delete,get,pull,push} ...
```

**命令**:  
* `create`              创建一个存储对象  
* `list (ls)`           显示存储对象列表  
* `delete`              删除一个存储对象  
* `get`                 显示一个存储对象的详细信息  
* `pull`                推送一个存储对象到[EverAI](everai.expvent.com)平台  
* `push`                从[EverAI](everai.expvent.com)平台拉取一个存储对象到本地  

**选项**:  
* `-h, --help`            显示帮助信息

## everai volume create              
创建一个存储对象  

**Example**:  
```bash
everai volume create test-volume
```

**Usage**:  
```bash
everai volume create [-h] name
```

**Positional arguments**:  

  * `name`        存储对象名称

**Options**:  

 * `-h, --help`  显示帮助信息

## everai volume list (ls)           
显示存储对象列表  

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
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table  

## everai volume delete              
删除一个存储对象  

**示例**:  
```bash 
everai volume delete get-start-volume --all
```

**格式**:   
```bash 
everai volume delete [-h] [--local] [--cloud] [--all] name
```

**Positional arguments**:
  * `name`        存储对象名称

**选项**:
  * `-h, --help`  显示帮助信息  
  * `--local`     只删除本地的存储对象  
  * `--cloud`     只删除平台云端的存储对象  
  * `--all`       同时删除本地和平台云端的存储对象  

## everai volume get                 
显示一个存储对象的详细信息  

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
  * `name`        存储对象名称

**选项**:    
* `-h, --help`  显示帮助信息

## everai volume pull                
推送一个存储对象到[EverAI](everai.expvent.com)平台

**示例**:  
```bash
everai volume pull get-start-volume
```
**格式**:  
```bash   
everai volume pull [-h] [--force] [--sync] name
```

**参数**:  
  * `name`        存储对象名称  

**选项**:  
* `-h, --help`  显示帮助信息  
* `--force`     强制从远端拉取存储对象文件到本地，系统不会比对`metadata`文件版本。如果不加`--force`，如果你本地的`metadata`文件版本和远端的一致，拉取会停止。    
* `--sync`      从远端同步文件，如果你的文件在本地存在，但在远端已经不存在，同步后这个本地文件会被删除。注意：需要和`--force`一起使用, `--sync`才会生效。    

## everai volume push                
从[EverAI](everai.expvent.com)平台拉取一个存储对象到本地  

**示例**:  
```bash
everai volume push get-start-volume
```

**格式**:
```bash   
everai volume push [-h] name
```
**参数**:  
  * `name`        存储对象名称  

**选项**:  
* `-h, --help`  显示帮助信息

