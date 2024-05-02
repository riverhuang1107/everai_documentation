# everai image
**Usage**:  
```bash
everai image [-h] {build} ...
```

**Positional arguments**:  
  {build}     everai image command helps  
* `build`     Image build

**Options**:  
* `-h, --help`  show this help message and exit  

## everai image build    
Image build  
**Examples**:  
This step will build the container image, using two very simple files Dockerfile and image_builder.py, and call the following command will compile the image and push them to your specified registry.  
The dependence of this step is docker and buildx installed on your machine. Otherwise we will have further prompts to help you install them
```bash
docker login quary.io  
everai image build
```

首先确认docker login是否成功。然后确认repo地址是否正确。  
image_builder.py
```python
from everai.image import Builder

#Example: quay.io/riverhuang1107/get-start:v0.0.30
IMAGE = 'quay.io/<username>/<repo>:<tag>'
```

**Usage**:  
```bash
everai image build [-h]
```
**Options**:  
  * `-h, --help`  show this help message and exit

