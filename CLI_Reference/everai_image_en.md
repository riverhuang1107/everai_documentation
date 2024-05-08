# everai image
Image management  

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

Build the container image, using two very simple files `Dockerfile` and `image_builder.py`.  

In `image_builder.py`, you should set your image repo.
```python
from everai.image import Builder

IMAGE = 'quay.io/<username>/<repo>:<tag>'
```
The dependence of building image is docker and buildx installed on your machine. Otherwise we will have further prompts to help you install them.  
```bash
docker login quay.io  

docker buildx version
```

Then call the following command will compile the image and push them to your specified registry.  
```bash
everai image build
```

**Usage**:  
```bash
everai image build [-h]
```
**Options**:  
  * `-h, --help`  show this help message and exit

