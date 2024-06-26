# everai configmap
Manage configmaps  

**Usage**:  
```bash
everai configmap [-h] {create,delete,list,ls,get,update} ...
```

**Commands**:  

* `create`              Create ConfigMap from file or literal string  
* `delete`              Delete configmap  
* `list (ls)`           List configmaps  
* `get`                 Get configmap  
* `update`              Update ConfigMap from file or literal string  

**Options**:  
* `-h, --help`            show this help message and exit  
 
## everai configmap create              
Create ConfigMap from file or literal string  

**Example**:  

Create a configmap from literal.  

```bash  
everai configmap create test-configmap \ 
  --from-literal min_workers=1 \
  --from-literal max_workers=5 \
  --from-literal max_queue_size=2 \
  --from-literal scale_up_step=1 \
  --from-literal max_idle_time=60
```
Create a configmap from a `yaml` file.  

Create a `yaml` file named test-configmap firstly, the data of the example like this: 
```bash
name: test-configmap
data:
  max_idle_time: '60'
  max_queue_size: '2'
  max_workers: '5'
  min_workers: '1'
  scale_up_step: '1'
```
Then, run the command like this:  
```bash  
everai configmap create --from-file test-configmap.yml test-configmap
```

**Usage**: 
```bash 
everai configmap create [-h] [-f FROM_FILE] [-l FROM_LITERAL] [name]
```

**Positional arguments**:  
  * `name`                  The configmap name

**Options**:  
* `-h, --help`            show this help message and exit  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`
                        Create configmap from literal, for example: `--from-literal name=user`  
* `-f FROM_FILE, --from-file FROM_FILE`
                        Create configmap from file, for example: `--from-file filename`  

## everai configmap delete              
Delete configmap  

**Example**:  
```bash
everai configmap delete test-configmap
```
**Usage**: 
```bash
everai configmap delete [-h] name
```

**Positional arguments**:  
  * `name`        The configmap name

**Options**:
* `-h, --help`  show this help message and exit

## everai configmap list (ls)           
List configmaps  

**Example**:  
```bash
everai configmap list
```
The result could be shown like this:  
```bash
NAME                    ITEMS
--------------------  -------
get-start-configmap         5
test-configmap              5
```

**Usage**: 
```bash
everai configmap list [-h] [--output [OUTPUT]]
```
**Options**:  
* `-h, --help`            show this help message and exit  
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)  

## everai configmap get                 
Get configmap  

**Example**:  
```bash  
everai configmap get test-configmap
```

**Usage**:  
```bash
everai configmap get [-h] [--output [OUTPUT]] name  
```

**Positional arguments**:  
  * `name`                  The configmap name

**Options**:  
* `-h, --help`            show this help message and exit  
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)

## everai configmap update              
Update ConfigMap from file or literal string  

**Example**:  

Update a configmap from literal.  

```bash  
everai configmap update --from-literal min_workers=2 \
  --from-literal max_workers=5 \
  --from-literal max_queue_size=2 \
  --from-literal scale_up_step=1 \
  --from-literal max_idle_time=60 test-configmap
```
Update a configmap from a `yaml` file.  

Create a `yaml` file named test-configmap firstly, the data of the example like this: 
```bash
name: test-configmap
data:
  max_idle_time: '60'
  max_queue_size: '2'
  max_workers: '5'
  min_workers: '2'
  scale_up_step: '1'
```

Then, run the command like this:  
```bash  
everai configmap update --from-file test-configmap.yml test-configmap
```

After `everai configmap update` is executed, applications using this updated ConfigMap will update relevant policies. For example, if you update `min_workers` from 1 to 2, the system will expand the minimum number of running workers for the related applications to 2.  

**Usage**: 
```bash
everai configmap update [-h] [-f FROM_FILE] [-l FROM_LITERAL] [name]
```

**Positional arguments**:  
  * `name`                  The configmap name

**Options**:  
* `-h, --help`            show this help message and exit  
* `-l FROM_LITERAL, --from-literal FROM_LITERAL`  Update configmap from literal, for example: `--from-literal name=user`
* `-f FROM_FILE, --from-file FROM_FILE`  Update configmap from file, for example: `--from-file filename`