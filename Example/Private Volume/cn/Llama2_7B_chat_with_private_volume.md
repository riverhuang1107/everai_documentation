#  使用私有卷搭建Llama-2-7b模型问答服务

在快速入门中，你已经创建了一个简单的应用。在这个示例中，我们使用私有卷存放`Llama-2(7B)`模型文件，实现一个基于`Llama-2(7B)`模型的文生文在线问答服务。  

## 创建应用

首先，为你的应用创建一个目录，进入应用目录后，首先需要使用你从[EverAI](https://everai.expvent.com)获取到的token进行登录。登录成功后，使用`everai app create`命令创建你的应用。  

```bash
everai login --token <your token>
```

## 创建密钥
  
如果你已经为你需要访问的镜像仓库创建过密钥，或者你的镜像仓库可以被公开访问，这一步是可选的。  

在这个例子中，我们会为[quay.io](https://quay.io/)创建一个密钥。  

```bash
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```
>**小贴士**  
>
>[quay.io](https://quay.io/)是一个知名的公共镜像仓库，与之类似的知名镜像仓库还有[Docker Hub](https://hub.docker.com/)，[GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)，[Google Container Registry](https://cloud.google.com/artifact-registry)等。  

此外，还需要为访问[Hugging Face](https://huggingface.co/)的token设置一个密钥。
```bash
everai secret create your-huggingface-secret-name \
  --from-literal token-key-as-your-wish=<your huggingface token>
```

## 编写你的代码
### 基本设置

这是一个关于[app.py](https://github.com/everai-example/llama2-7b-chat-with-private-volume/blob/main/app.py)的示例代码。  

首先，引入必要的EverAI Python类库。然后定义所需要用到的变量名，包括卷，访问镜像仓库的密钥，以及存放在卷中的文件等。使用`Image.from_registry`静态方法创建一个镜像实例。通过App类来创建定义一个app实例。  

这里需要注意的是，你需要为你的应用配置GPU资源，这里配置的GPU型号是"A100 40G"，GPU卡的数量是1。  

```python
from everai.app import App, context, VolumeRequest
from everai_autoscaler.builtin import SimpleAutoScaler
from everai.image import Image, BasicAuth
from everai.resource_requests import ResourceRequests
from everai.placeholder import Placeholder
from image_builder import IMAGE

APP_NAME = '<your app name>'
VOLUME_NAME = 'models--meta-llama--llama-2-7b-chat-hf'
MODEL_NAME = 'meta-llama/Llama-2-7b-chat-hf'
HUGGINGFACE_SECRET_NAME = 'your-huggingface-secret-name'
QUAY_IO_SECRET_NAME = 'your-quay-io-secret-name'
CONFIGMAP_NAME = 'llama2-configmap'

image = Image.from_registry(IMAGE, auth=BasicAuth(
        username=Placeholder(QUAY_IO_SECRET_NAME, 'username', kind='Secret'),
        password=Placeholder(QUAY_IO_SECRET_NAME, 'password', kind='Secret'),
    ))

app = App(
    APP_NAME,
    image=image,
    volume_requests=[
        VolumeRequest(name=VOLUME_NAME, create_if_not_exists=True),
    ],
    secret_requests=[
        HUGGINGFACE_SECRET_NAME,
        QUAY_IO_SECRET_NAME,
    ],
    configmap_requests=[CONFIGMAP_NAME],
    autoscaler=SimpleAutoScaler(
        # keep running workers even no any requests, that make reaction immediately for new request
        min_workers=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='min_workers'),
        # the maximum works setting, protect your application avoid to pay a lot of money
        # when an attack or sudden traffic
        max_workers=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='max_workers'),
        # this factor controls autoscaler how to scale up your app
        max_queue_size=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='max_queue_size'),
        # this factor controls autoscaler how to scale down your app
        max_idle_time=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='max_idle_time'),
        # this factor controls autoscaler how many steps to scale up your app from queue 
        scale_up_step=Placeholder(kind='ConfigMap', name=CONFIGMAP_NAME, key='scale_up_step'),
    ),
    resource_requests=ResourceRequests(
        cpu_num=2,
        memory_mb=20480,
        gpu_num=1,
        gpu_constraints=[
            "A100 40G"
        ],
    ),
)
```

### 预加载模型

如果你的本地环境没有模型文件，你可以使用`LlamaForCausalLM.from_pretrained`方法传入`MODEL_NAME`，从[Hugging Face](https://huggingface.co/)官网拉取模型文件。并且会通过设置`cache_dir`，把模型文件缓存到私有卷`models--meta-llama--llama-2-7b-chat-hf`中。  

你可以通过`everai volume get`命令获取到卷`models--meta-llama--llama-2-7b-chat-hf`的本地路径。进入卷的本地路径后，可以看到已经被缓存的模型文件。  

```bash
everai volume get models--meta-llama--llama-2-7b-chat-hf
<Volume: id: UoswTkyjkPZU3qb27s4B9E, name: models--meta-llama--llama-2-7b-chat-hf, revision: 000001-821, files: 21, size: 25.11 GiB>
path: /root/.cache/everai/volumes/UoswTkyjkPZU3qb27s4B9E
```
你在本地环境使用`everai app run`调试示例代码时，`is_prepare_mode`的值是`False`，不会执行把本地文件推送到云端的操作。如果你的本地调试环境有GPU资源，系统会成功执行`model.cuda(0)`。

待你的代码调试通过后，执行`everai app prepare`命令，该命令会执行所有被`@app.prepare`注解过的方法，此时`is_prepare_mode`的值是`True`，在示例代码中，卷`models--meta-llama--llama-2-7b-chat-hf`在本地环境中的模型文件会在执行该命令时被推送到云端。

```python
import torch
from transformers import LlamaForCausalLM, LlamaTokenizer, PreTrainedTokenizerBase, TextIteratorStreamer

@app.prepare()
def prepare_model():
    volume = context.get_volume(VOLUME_NAME)
    assert volume is not None and volume.ready

    secret = context.get_secret(HUGGINGFACE_SECRET_NAME)
    assert secret is not None
    huggingface_token = secret.get('token-key-as-your-wish')

    model_dir = volume.path

    global model
    global tokenizer

    # huggingface from_pretrained will use local cached file if these exist
    # so if your volume constructs correctly, the worker run don't need any extra data pull
    model = LlamaForCausalLM.from_pretrained(MODEL_NAME,
                                             token=huggingface_token,
                                             cache_dir=model_dir,
                                             torch_dtype=torch.float16,
                                             local_files_only=context.is_in_cloud)
    
    tokenizer = LlamaTokenizer.from_pretrained(MODEL_NAME,
                                               token=huggingface_token,
                                               cache_dir=model_dir,
                                               local_files_only=context.is_in_cloud)

    # only in prepare mode push volume
    # to save gpu time (redundant sha256 checks)
    if context.is_prepare_mode:
        context.volume_manager.push(VOLUME_NAME)
    else:
        if torch.cuda.is_available():
            model.cuda(0)
```

### 实现推理服务

加载`Llama-2(7B)`模型后，这里的代码使用了`flask`实现了文生文的推理在线服务。  

```python
import flask
import typing

tokenizer: typing.Optional[PreTrainedTokenizerBase] = None
model = None

# service entrypoint
# api service url looks https://everai.expvent.com/api/routes/v1/default/llama2-7b-chat/chat
# for test local url is http://127.0.0.1/chat
@app.service.route('/chat', methods=['GET','POST'])
def chat():
    if flask.request.method == 'POST':
        data = flask.request.json
        prompt = data['prompt']
    else:
        prompt = flask.request.args["prompt"]

    input_ids = tokenizer.encode(prompt, return_tensors="pt")
    input_ids = input_ids.to('cuda:0')
    output = model.generate(input_ids, max_length=256, num_beams=4, no_repeat_ngram_size=2)
    response = tokenizer.decode(output[0], skip_special_tokens=True)

    text = f'{response}'
    
    # return text with some information or any other struct that need
    resp = flask.Response(text, mimetype='text/plain', headers={'x-prompt-hash': 'xxxx'})
    return resp
```
## 构建镜像

这步需要使用`Dockerfile`和`image_builder.py`来为你的应用构建容器镜像。  

这是一个关于[image_builder.py](https://github.com/everai-example/llama2-7b-chat-with-private-volume/blob/main/image_builder.py)的示例代码。  

在`image_builder.py`中，你需要配置你的镜像地址信息。  

在这个示例中，我们选择使用[quay.io](https://quay.io/)作为公共镜像仓库，存放应用镜像。你也可以使用与之类似的知名镜像仓库，如：[Docker Hub](https://hub.docker.com/)，[GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)，[Google Container Registry](https://cloud.google.com/artifact-registry)等。如果你有自建的镜像仓库，并且镜像可以在互联网上被访问，同样可以使用。

```python
from everai.image import Builder

IMAGE = 'quay.io/<username>/<repo>:<tag>'
```

首先确保你的docker环境处于登录状态，以及你已经安装了docker buildx插件。  
```bash
docker login quay.io
docker buildx version
```

然后执行以下命令打包镜像，并且把打包好的镜像推送到你指定的镜像仓库中。  
```bash
everai image build
```

## 部署

最后一步是把你的应用部署到EverAI。并使它保持在运行状态。  
```bash
everai app create
```

执行`everai app list`后，可以看到类似如下的输出结果。如果你的应用状态是`DEPLOYED`，意味着你的应用已经部署成功。  

```bash
NAME                           STATUS    CREATED_AT                ROUTE_NAME
-----------------------------  --------  ------------------------  -----------------------------
llama2-7b-chat                 DEPLOYED  2024-05-28 22:55:16+0800  llama2-7b-chat
```

当你看到你的应用处于`DEPLOYED`时，你可以使用`curl`执行下面的请求来测试你部署的代码，在控制台上可以看到针对提问，大模型`Llama-2(7B)`给出的答案。显示如下的数据信息。  

```bash
curl -X POST -d '{"prompt": "who are you"}' -H 'Content-Type: application/json' -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/chat
who are you?

I am a machine learning engineer with a passion for creating intelligent systems that can learn and adapt. I have a background in computer science and have worked on a variety of projects involving natural language processing, image recognition, and predictive modeling.
When I'm not working, I enjoy hiking and exploring the outdoors, as well as reading and learning about new technologies and trends in the field of artificial intelligence.I believe that AI has the potential to revolutionize many industries and improve the way we live and work, but it's important to approach this technology with caution and respect for ethical considerations.
```




