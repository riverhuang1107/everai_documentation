# EverAI Chat with Llama-2(7B)

在快速入门中，你已经创建了一个简单的应用。在这个示例中，我们使用`Llama-2(7B)`模型来实现一个文生文的在线问答服务。  

## 创建应用

首先，为你的应用创建一个目录，进入应用目录后，首先需要使用你从[EverAI](https://everai.expvent.com)获取到的token进行登录。登录成功后，使用`everai app create`命令创建你的应用。  

```bash
everai login --token <your token>

everai app create <your app name>
```

## 创建密钥
  
如果你已经为你的镜像仓库创建过密钥，这一步可以跳过。  

在这个例子中，我们会为[quay.io](https://quay.io/)创建一个密钥。  

```bash
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```

## 编写你的代码
### 基本设置

这是一个关于app.py的示例代码。  

首先，引入必要的EverAI Python类库。然后定义所需要用到的变量名，包括对象存储，访问镜像仓库的密钥，以及存放在对象存储中的文件等。使用`Image.from_registry`静态方法创建一个镜像实例。通过App类来创建定义一个app实例。  

这里需要注意的是，你需要为你的应用配置GPU资源，这里配置的GPU型号是"A100 40G"，GPU卡的数量是1。  

```python
from everai.app import App, context, VolumeRequest
from everai.autoscaling import SimpleAutoScalingPolicy
from everai.image import Image, BasicAuth
from everai.resource_requests import ResourceRequests
from everai.placeholder import Placeholder

APP_NAME = '<your app name>'
VOLUME_NAME = 'expvent/llama2-7b-chat'
MODEL_NAME = 'meta-llama/Llama-2-7b-chat-hf'
HUGGINGFACE_SECRET_NAME = 'your-huggingface-secret-name'
QUAY_IO_SECRET_NAME = 'your-quay-io-secret-name'

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
    autoscaling_policy=SimpleAutoScalingPolicy(
        # keep running workers even no any requests, that make reaction immediately for new request
        min_workers=1,
        # the maximum works setting, protect your application avoid to pay a lot of money
        # when an attack or sudden traffic
        max_workers=10,
        # this factor control autoscaler how to scale up your app
        max_queue_size=3,
        # this factor control autoscaler how to scale down your app
        max_idle_time=60,
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

你可以使用我们提供的公开存储对象`expvent/llama2-7b-chat`中的模型文件加载模型。 

```python
@app.prepare()
def prepare_model():
    print(context.volumes)
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
    #model = LlamaForCausalLM.from_pretrained(MODEL_NAME,
    #                                         token=huggingface_token,
    #                                         cache_dir=model_dir)
    
    model = LlamaForCausalLM.from_pretrained(model_dir, torch_dtype=torch.float16, local_files_only=True)
    model.cuda(0)
    print('model success loaded')

    #tokenizer = LlamaTokenizer.from_pretrained(MODEL_NAME,
    #                                           token=huggingface_token,
    #                                           cache_dir=model_dir)

    tokenizer = LlamaTokenizer.from_pretrained(model_dir, local_files_only=True)
    print('tokenizer success loaded')
```

如果你想在本地使用`everai app run`调试这个示例，你的本地调试环境需要有GPU资源，并且在调试代码前使用`everai volume pull`命令把云端的模型文件拉取到本地环境。  
```bash
everai volume pull expvent/llama2-7b-chat
```

### 实现推理服务

加载`Llama-2(7B)`模型后，这里的代码使用了`flask`实现了文生文的推理在线服务。  

```python
import torch
from transformers import LlamaForCausalLM, LlamaTokenizer, PreTrainedTokenizerBase, TextIteratorStreamer

import flask
import typing

tokenizer: typing.Optional[PreTrainedTokenizerBase] = None
model = None

# service entrypoint
# api service url looks https://everai.expvent.com/api/apps/v1/routes/llama2-7b-chat-example/chat
# for test local url is http://127.0.0.1:8866/chat
@app.service.route('/chat', methods=['POST'])
def chat():
    data = flask.request.json
    prompt = data['prompt']

    print(f'prompt: {prompt}')

    input_ids = tokenizer.encode(prompt, return_tensors="pt")
    input_ids = input_ids.to('cuda:0')
    output = model.generate(input_ids, max_length=256, num_beams=4, no_repeat_ngram_size=2)
    response = tokenizer.decode(output[0], skip_special_tokens=True)
    print(f'response: {response}')

    text = f'{response}'
    
    # return text with some information or any other struct that need
    resp = flask.Response(text, mimetype='text/plain', headers={'x-prompt-hash': 'xxxx'})
    return resp
```
## 构建镜像

这步需要使用`Dockerfile`和`image_builder.py`来为你的应用构建容器镜像。  

在`image_builder.py`中，你需要配置你的镜像地址信息。  

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
everai app deploy
```

执行`everai app list`后，可以看到类似如下的输出结果。如果你的应用状态是`DEPLOYED`，意味着你的应用已经部署成功。  

```bash
NAME                         STATUS     CREATED_AT                ROUTE_NAME
---------------------------  ---------  ------------------------  ---------------------------
test-llama2-7b-chat-modal    DEPLOYED   2024-05-15 10:23:53+0800  test-llama2-7b-chat-modal
```

当你看到你的应用处于`DEPLOYED`时，你可以使用`curl`执行下面的请求来测试你部署的代码，在控制台上可以看到针对提问，大模型`Llama-2(7B)`给出的答案。显示如下的数据信息。  

```bash
curl -X POST -d '{"prompt": "who are you"}' -H 'Content-Type: application/json' -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/apps/v1/routes/<your app route name>/chat
who are you?

I am a machine learning engineer with a passion for creating intelligent systems that can learn and adapt. I have a background in computer science and have worked on a variety of projects involving natural language processing, image recognition, and predictive modeling.
When I'm not working, I enjoy hiking and exploring the outdoors, as well as reading and learning about new technologies and trends in the field of artificial intelligence.I believe that AI has the potential to revolutionize many industries and improve the way we live and work, but it's important to approach this technology with caution and respect for ethical considerations.
```




