# everai namespace
命名空间管理  

**Usage**:   
```bash 
everai namespace [-h] {list,ls,get,create,delete} ...  
```

**Commands**:    
* `list (ls)` 显示命名空间列表  
* `get`       显示命名空间信息          
* `create`    创建一个命名空间
* `delete`    删除一个命名空间

**Options**:  
* `-h, --help`  显示帮助信息  

## everai namespace list (ls)
显示命名空间列表  

**Example**:  
```bash 
everai namespace list
```

用例输出的结果如下所示：

```bash 
NAME     CREATED_AT
-------  ------------------------
default  2024-04-18T16:00:00+0000
pre      2024-06-25T12:52:56+0000
```

**Usage**:  
```bash  
everai namespace list [-h] [--output [OUTPUT]]
```

**Options**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table  

## everai namespace get
显示命名空间信息   

**Example**:  
```bash 
everai namespace get devops
```

用例输出的结果如下所示：  

```bash 
NAME    CREATED_AT
------  ------------------------
devops  2024-06-29T07:33:53+0000
```

**Usage**:  
```bash  
everai namespace get [-h] [--output [OUTPUT]] [name]
```

**Positional arguments**:  
  * `name`              命名空间名称  

**Options**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table 

## everai namespace create
创建一个命名空间   

**Example**:  
```bash 
everai namespace create devops
```

**Usage**:  
```bash  
everai namespace create [-h] [--output [OUTPUT]] [name]
```

**Positional arguments**:  
  * `name`              命名空间名称  

**Options**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table

## everai namespace delete
删除一个命名空间   

**Example**:  
```bash 
everai namespace delete devops
```

**Usage**:  
```bash  
everai namespace delete [-h] name
```

**Positional arguments**:  
  * `name`              命名空间名称  

**Options**:  
* `-h, --help`            显示帮助信息  
