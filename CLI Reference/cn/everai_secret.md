# everai secret
管理密钥  

**格式**:  
```bash
everai secret [-h] {create,delete,list,ls,get,update} ...
```

**命令**:  
* `create`              通过配置文件或者字符串键值对创建密钥 
* `delete`              删除密钥 
* `list (ls)`           显示密钥列表 
* `get`                 根据密钥名称查询密钥 
* `update`              通过配置文件或者字符串键值对更新密钥 

**选项**:  
* `-h, --help`            显示帮助信息  

## everai secret create              
通过配置文件或者字符串键值对创建密钥

**示例**:  

通过命令行设置字符串键值对创建一个密钥。  

```bash  
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```

通过一个`yaml`配置文件创建一个密钥。  

首先创建一个名为test-secret的`yaml`文件，文件内容中的数据值需要先经过base64编码，文件内容如下所示：  

```bash
name: your-quay-io-secret-name
data:
  password: YmFyCg==
  username: Zm9vCg==
```
然后，运行如下所示命令创建密钥。 

```bash  
everai secret create --from-file test-secret.yml your-quay-io-secret-name
```

**格式**:
```bash  
everai secret create [-h] [-f FROM_FILE] [-l FROM_LITERAL] [name]  
```

**参数**:  
  * `name`                  密钥名称

**选项**:  
* `-h, --help`            显示帮助信息  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`
                        通过命令行设置字符串键值对的方式创建密钥，举例：`--from-literal name=user` 
* `-f FROM_FILE, --from-file FROM_FILE`
                        通过配置文件的方式创建密钥，举例：`--from-file filename`  

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

用例输出的结果如下所示：  

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
通过配置文件或者字符串键值对更新密钥  

**示例**:  

通过命令行设置字符串键值对更新一个密钥。  

```bash
everai secret update \
  --from-literal username=<your username> \
  --from-literal password=<your password> your-quay-io-secret-name
```

通过一个`yaml`配置文件更新一个密钥。  

首先创建一个名为test-secret的`yaml`文件，文件内容中的数据值需要先经过base64编码，文件内容如下所示：  

```bash
name: your-quay-io-secret-name
data:
  password: YmFyCg==
  username: Zm9vCg==
```
然后，运行如下所示命令更新密钥。 
 
```bash  
everai secret update --from-file test-secret.yml your-quay-io-secret-name
```


**格式**:  
```bash
everai secret update [-h] [-f FROM_FILE] [-l FROM_LITERAL] [name]
```
**参数**:  
  * `name`                  密钥名称  

**选项**:  
* `-h, --help`            显示帮助信息  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`
                        通过命令行设置字符串键值对的方式更新密钥，举例：`--from-literal name=user`  
* `-f FROM_FILE, --from-file FROM_FILE`
                        通过配置文件的方式更新密钥，举例：`--from-file filename`。文件类型支持简单的键值对的YAML文件，值必须是字符串  

