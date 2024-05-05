# everai image
**Usage**:  
```bash
everai image [-h] {build} ...
```

**Commands**:  
* `build`     Image build

**Options**:  
* `-h, --help`  show this help message and exit  

## everai image build    
Image build  

**Example**:  

构建容器镜像，需要使用`Dockerfile`和`image_builder.py`来为你的应用构建容器镜像。  

在`image_builder.py`中，你需要配置你的镜像地址信息。

```python
from everai.image import Builder

IMAGE = 'quay.io/<username>/<repo>:<tag>'
```
首先确保你的docker环境处于登录状态，以及你已经安装了docker buildx插件。  

```bash
docker login quary.io  

docker buildx version
```

然后执行以下命令打包镜像，并且把打包好的镜像推送到你指定的镜像仓库中。  

```bash
everai image build
```

**Usage**:  
```bash
everai image build [-h]
```
**Options**:  
  * `-h, --help`  show this help message and exit

