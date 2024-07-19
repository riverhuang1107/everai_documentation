# everai namespace
命名空间管理  

**格式**:   
```bash 
everai namespace [-h] {list,ls,get,create,delete} ...  
```

**命令**:    
* `list (ls)` 显示命名空间列表  
* `get`       显示命名空间信息          
* `create`    创建一个命名空间
* `delete`    删除一个命名空间

**选项**:  
* `-h, --help`  显示帮助信息  

## everai namespace list (ls)
显示命名空间列表  

**示例**:  
```bash 
everai namespace list
```

用例输出的结果如下所示，`CREATED_AT`使用UTC时间显示。

```bash 
NAME     CREATED_AT
-------  ------------------------
default  2024-04-18T16:00:00+0000
pre      2024-06-25T12:52:56+0000
```

**格式**:  
```bash  
everai namespace list [-h] [--output [OUTPUT]]
```

**选项**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table  

## everai namespace get
显示命名空间信息   

**示例**:  
```bash 
everai namespace get devops
```

用例输出的结果如下所示，`CREATED_AT`使用UTC时间显示。  

```bash 
NAME    CREATED_AT
------  ------------------------
devops  2024-06-29T07:33:53+0000
```

**格式**:  
```bash  
everai namespace get [-h] [--output [OUTPUT]] [name]
```

**参数**:  
  * `name`              命名空间名称  

**选项**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table 

## everai namespace create
创建一个命名空间   

**示例**:  
```bash 
everai namespace create devops
```

**格式**:  
```bash  
everai namespace create [-h] [name]
```

**参数**:  
  * `name`              命名空间名称  

**选项**:  
* `-h, --help`            显示帮助信息  

## everai namespace delete
删除一个命名空间   

**示例**:  
```bash 
everai namespace delete devops
```

**格式**:  
```bash  
everai namespace delete [-h] name
```

**参数**:  
  * `name`              命名空间名称  

**选项**:  
* `-h, --help`            显示帮助信息  
