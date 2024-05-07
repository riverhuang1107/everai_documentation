# everai image
**格式**:  
```bash
everai image [-h] {build} ...
```

**命令**:  
* `build`     构建镜像

**选项**:  
* `-h, --help`  显示帮助信息  

## everai image build    
构建应用镜像  

**示例**:  

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

**格式**:  
```bash
everai image build [-h]
```
**选项**:  
  * `-h, --help`  显示帮助信息

