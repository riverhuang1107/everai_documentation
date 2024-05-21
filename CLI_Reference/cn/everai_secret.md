# everai secret
管理密钥  

**格式**:  
```bash
everai secret [-h] {create,delete,list,ls,get,update} ...
```

**命令**:  
* `create`              从文件或者字符串创建密钥 
* `delete`              删除密钥 
* `list (ls)`           显示密钥列表 
* `get`                 根据密钥名称查询密钥 
* `update`              从文件或者字符串更新密钥 

**选项**:  
* `-h, --help`            显示帮助信息  

## everai secret create              
从文件或者字符串创建密钥

**示例**:  

从字符串创建一个密钥。  

```bash  
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```

通过一个`yaml`文件创建一个密钥。  

首先创建一个名为test-secret的`yaml`文件，文件内容如下所示：  

```bash
username: foo
password: bar
```
然后，运行如下所示命令创建密钥。 

```bash  
everai secret create --from-file test-secret.yml your-quay-io-secret-name
```

**格式**:
```bash  
everai secret create [-h] [-l FROM_LITERAL] [-f FROM_FILE] name  
```

**参数**:  
  * `name`                  密钥名称

**选项**:  
* `-h, --help`            显示帮助信息  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`
                        从字符串创建密钥，举例：`--from-literal name=user` 
* `-f FROM_FILE, --from-file FROM_FILE`
                        通过文件创建密钥，举例：`--from-file filename`  

## everai secret delete              
删除密钥

**示例**:  
```bash
everai secret delete quay-secret
```

**格式**:   
```bash
everai secret delete [-h] name
```
**参数**:  
  * `name`        密钥名称

**选项**:  
* `-h, --help`  显示帮助信息  

## everai secret list (ls)           
显示密钥列表  

**示例**:
```bash  
everai secret list
```
The result could be shown like this:  
```bash 
NAME                        ITEMS
------------------------  -------
quay-secret                     2
your-quay-io-secret-name        2
```
 
**格式**:
```bash  
everai secret list [-h] [--output [OUTPUT]]  
```

**选项**:  
* `-h, --help`            显示帮助信息
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table

## everai secret get                 
根据密钥名称查询密钥

**示例**:
```bash  
everai secret get your-huggingface-secret-name
```
**格式**:
```bash  
everai secret get [-h] [--output [OUTPUT]] name
```

**参数**:  
  * `name`                  密钥名称  

**选项**:  
* `-h, --help`            显示帮助信息  
* `--output [OUTPUT], -o [OUTPUT]`
                        输出格式，可选（json, yaml, table, wide），默认为table  

## everai secret update              
从文件或者字符串更新密钥  

**示例**:  

从字符串更新一个密钥。  

```bash
everai secret update \
  --from-literal username=<your username> \
  --from-literal password=<your password> your-quay-io-secret-name
```

通过一个`yaml`文件创建一个密钥。  

首先创建一个名为test-secret的`yaml`文件，文件内容如下所示：  

```bash
username: bar
password: foo
```
然后，运行如下所示命令更新密钥。 
 
```bash  
everai secret update --from-file test-secret.yml your-quay-io-secret-name
```


**格式**:  
```bash
everai secret update [-h] [-l FROM_LITERAL] [-f FROM_FILE] name
```
**参数**:  
  * `name`                  密钥名称  

**选项**:  
* `-h, --help`            显示帮助信息  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`
                        通过字符串更新密钥，举例：`--from-literal name=user`  
* `-f FROM_FILE, --from-file FROM_FILE`
                        通过文件更新密钥，举例：`--from-file filename`。文件类型支持简单的键值对的YAML文件，值必须是字符串  

