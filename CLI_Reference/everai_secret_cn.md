# everai secret
**格式**:  
```bash
everai secret [-h] {create,delete,list,ls,get,update} ...
```

**命令**:  
* `create`              Create Secret from file or literal string  
* `delete`              Delete secret  
* `list (ls)`           List secret  
* `get`                 Get secret  
* `update`              Update the Secret from file or literal string  

**选项**:  
* `-h, --help`            show this help message and exit  

## everai secret create              
Create Secret from file or literal string  

**示例**:  
```bash  
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```

**格式**:
```bash  
everai secret create [-h] [-l FROM_LITERAL] [-f FROM_FILE] name  
```

**参数**:  
  * `name`                  The secret name

**选项**:  
* `-h, --help`            show this help message and exit  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`
                        Create secret from literal, for example: --from-literal name=user  
* `-f FROM_FILE, --from-file FROM_FILE`
                        Create secret from file, for example: --from-file filename  

## everai secret delete              
Delete secret  

**示例**:  
```bash
everai secret delete quay-secret
```

**格式**:   
```bash
everai secret delete [-h] name
```
**参数**:  
  * `name`        The secret name

**选项**:  
* `-h, --help`  show this help message and exit  

## everai secret list (ls)           
List secret  

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
* `-h, --help`            show this help message and exit
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)

## everai secret get                 
Get secret  

**示例**:
```bash  
everai secret get your-huggingface-secret-name
```
**格式**:
```bash  
everai secret get [-h] [--output [OUTPUT]] name
```

**参数**:  
  * `name`                  The secret name  

**选项**:  
* `-h, --help`            show this help message and exit  
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)  

## everai secret update              
Update the Secret from file or literal string  

**示例**:  
```bash
everai secret update \
  --from-literal username=<your username> \
  --from-literal password=<your password> your-quay-io-secret-name
```

**格式**:  
```bash
everai secret update [-h] [-l FROM_LITERAL] [-f FROM_FILE] name
```
**参数**:  
  * `name`                  The secret name  

**选项**:  
* `-h, --help`            show this help message and exit  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`
                        Create secret from literal, for example: --from-literal name=user  
* `-f FROM_FILE, --from-file FROM_FILE`
                        Create secret from file, for example: --from-file filename, file is yaml for simply key value,
                        all value should be string  

