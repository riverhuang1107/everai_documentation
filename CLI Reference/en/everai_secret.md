# everai secret
Manage secrets  

**Usage**:  
```bash
everai secret [-h] {create,delete,list,ls,get,update} ...
```

**Commands**:  
* `create`              Create Secret from file or literal string  
* `delete`              Delete secret  
* `list (ls)`           List secret  
* `get`                 Get secret  
* `update`              Update the Secret from file or literal string  

**Options**:  
* `-h, --help`            show this help message and exit  

## everai secret create              
Create Secret from file or literal string  

**Example**:  

Create a Secret from literal.  

```bash  
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```

Create a Secret from a `yaml` file.  

Create a `yaml` file named test-secret firstly, the data of the example like this: 
```bash
username: foo
password: bar
```
Then, run the command like this:  
```bash  
everai secret create --from-file test-secret.yml your-quay-io-secret-name
```

**Usage**:
```bash  
everai secret create [-h] [-f FROM_FILE] [-l FROM_LITERAL] [name]  
```

**Positional arguments**:  
  * `name`                  The secret name

**Options**:  
* `-h, --help`            show this help message and exit  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`
                        Create secret from literal, for example: `--from-literal name=user`  
* `-f FROM_FILE, --from-file FROM_FILE`
                        Create secret from file, for example: `--from-file filename`  

## everai secret delete              
Delete secret  

**Example**:  
```bash
everai secret delete quay-secret
```

**Usage**:   
```bash
everai secret delete [-h] name
```
**Positional arguments**:  
  * `name`        The secret name

**Options**:  
* `-h, --help`  show this help message and exit  

## everai secret list (ls)           
List secret  

**Example**:
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
 
**Usage**:
```bash  
everai secret list [-h] [--output [OUTPUT]]  
```

**Options**:  
* `-h, --help`            show this help message and exit
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)

## everai secret get                 
Get secret  

**Example**:
```bash  
everai secret get your-huggingface-secret-name
```
**Usage**:
```bash  
everai secret get [-h] [--output [OUTPUT]] name
```

**Positional arguments**:  
  * `name`                  The secret name  

**Options**:  
* `-h, --help`            show this help message and exit  
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)  

## everai secret update              
Update the Secret from file or literal string  

**Example**:  

Update a Secret from literal.  

```bash
everai secret update \
  --from-literal username=<your username> \
  --from-literal password=<your password> your-quay-io-secret-name
```

Update a Secret from a `yaml` file.  

Create a `yaml` file named test-secret firstly, the data of the example like this: 
```bash
username: bar
password: foo
```
Then, run the command like this:  
```bash  
everai secret update --from-file test-secret.yml your-quay-io-secret-name
```

**Usage**:  
```bash
everai secret update [-h] [-f FROM_FILE] [-l FROM_LITERAL] [name]
```
**Positional arguments**:  
  * `name`                  The secret name  

**Options**:  
* `-h, --help`            show this help message and exit  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`
                        Create secret from literal, for example: `--from-literal name=user`  
* `-f FROM_FILE, --from-file FROM_FILE`
                        Create secret from file, for example: `--from-file filename`, file is yaml for simply key value,
                        all value should be string  

