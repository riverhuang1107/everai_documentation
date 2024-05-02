everai secret
Usage: 
everai secret [-h] {create,delete,list,ls,get,update} ...

positional arguments:
  {create,delete,list,ls,get,update}
                        Secret command help
    create              Create Secret from file or literal string
    delete              Delete secret
    list (ls)           List secret
    get                 Get secret
    update              Update the Secret from file or literal string

Options:
-h, --help            show this help message and exit

everai secret create              
Create Secret from file or literal string
Examples:
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>

Usage: 
everai secret create [-h] [-l FROM_LITERAL] [-f FROM_FILE] name

positional arguments:
  name                  The secret name

Options:
-h, --help            show this help message and exit
-l FROM_LITERAL, --from-literal FROM_LITERAL
                        Create secret from literal, for example: --from-literal name=user
-f FROM_FILE, --from-file FROM_FILE
                        Create secret from file, for example: --from-file filename

everai secret delete              
Delete secret
Examples:
everai secret delete quay-secret

Usage: 
everai secret delete [-h] name

positional arguments:
  name        The secret name

Options:
-h, --help  show this help message and exit

everai secret list (ls)           
List secret
Examples:
everai secret list

Usage: 
everai secret list [-h] [--output [OUTPUT]]

Options:
-h, --help            show this help message and exit
--output [OUTPUT], -o [OUTPUT]
                        Output format, One of: (json, yaml, table, wide)

everai secret get                 
Get secret
Examples:
everai secret get your-huggingface-secret-name

Usage: 
everai secret get [-h] [--output [OUTPUT]] name

positional arguments:
  name                  The secret name

Options:
-h, --help            show this help message and exit
--output [OUTPUT], -o [OUTPUT]
                        Output format, One of: (json, yaml, table, wide)

everai secret update              
Update the Secret from file or literal string
Examples:
everai secret update \
  --from-literal username=<your username> \
  --from-literal password=<your password> your-quay-io-secret-name

拉取镜像需要docker login的密码，而不是quay.io网站的登录密码

Usage: 
everai secret update [-h] [-l FROM_LITERAL] [-f FROM_FILE] name

positional arguments:
  name                  The secret name

Options:
-h, --help            show this help message and exit
-l FROM_LITERAL, --from-literal FROM_LITERAL
                        Create secret from literal, for example: --from-literal name=user
-f FROM_FILE, --from-file FROM_FILE
                        Create secret from file, for example: --from-file filename, file is yaml for simply key value,
                        all value should be string

