# everai namespace
Manage the namespaces  

**Usage**:   
```bash 
everai namespace [-h] {list,ls,get,create,delete} ...  
```

**Commands**:    
* `list (ls)` List the namespaces  
* `get`                 Get the namespace
* `create`              Create a namespace
* `delete`              Delete a namespace

**Options**:  
* `-h, --help`  show this help message and exit  

## everai namespace list (ls)
List the namespaces  

**Example**:  
```bash 
everai namespace list
```
The result could be shown like this, and `CREATED_AT` uses UTC time display.  
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
* `-h, --help`            show this help message and exit  
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide)  

## everai namespace get
Get the namespace   

**Example**:  
```bash 
everai namespace get devops
```
The result could be shown like this:  
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
  * `name`              The namespace name  

**Options**:  
* `-h, --help`            show this help message and exit  
* `--output [OUTPUT], -o [OUTPUT]`
                        Output format, One of: (json, yaml, table, wide) 

## everai namespace create
Create a namespace   

**Example**:  
```bash 
everai namespace create devops
```

**Usage**:  
```bash  
everai namespace create [-h] [name]
```

**Positional arguments**:  
  * `name`              The namespace name  

**Options**:  
* `-h, --help`            show this help message and exit  

## everai namespace delete
Delete a namespace   

**Example**:  
```bash 
everai namespace delete devops
```

**Usage**:  
```bash  
everai namespace delete [-h] name
```

**Positional arguments**:  
  * `name`              The namespace name  

**Options**:  
* `-h, --help`            show this help message and exit  
