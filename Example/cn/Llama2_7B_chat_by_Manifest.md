#  使用manifest yaml文件搭建Llama-2-7b模型问答服务
在这个示例中，我们通过定义一个manifest yaml文件，即可在[EverAI](https://everai.expvent.com)平台上创建一个基于`Llama-2(7B)`模型的文生文在线问答服务。这种方法无需在你的已有的业务代码中引入EverAI SDK代码，便可以把你的应用部署到EverAI平台。

## 登录EVERAI客户端

首先，为你的应用创建一个目录，进入应用目录后，首先需要使用你从[EverAI](https://everai.expvent.com)获取到的token进行登录。    

```bash
everai login --token <your token>
```

## 准备存储

创建一个数据卷用于存放模型文件。  

```bash
everai volume create models--meta-llama--llama-2-7b-chat-hf
```

你可以通过`everai volume get`命令获取到卷`models--meta-llama--llama-2-7b-chat-hf`的本地路径。进入卷的本地路径后，可以把你的模型文件复制到该路径下。

通过`everai volume push`命令，把数据卷`models--meta-llama--llama-2-7b-chat-hf`在本地环境中的模型文件，推送到云端。

```bash
everai volume push models--meta-llama--llama-2-7b-chat-hf
```

在当前应用目录下，创建一个软连接来挂载卷`models--meta-llama--llama-2-7b-chat-hf`的本地路径。  

```bash
ln -s /root/.cache/everai/volumes/UoswTkyjkPZU3qb27s4B9E volume
```

## 编写你的代码

### 加载模型

这是一个关于[app.py](https://github.com/everai-example/llama2-7b-chat-manifest-private/blob/main/app.py)的示例代码。

你在本地环境使用`python run`调试示例代码加载模型时，模型文件会从本地环境的私有卷`models--meta-llama--llama-2-7b-chat-hf`中读取。如果你的本地调试环境有GPU资源，系统会成功执行`model.cuda(0)`。

```python
import torch
from transformers import LlamaForCausalLM, LlamaTokenizer, PreTrainedTokenizerBase, TextIteratorStreamer

MODEL_NAME = 'meta-llama/Llama-2-7b-chat-hf'
HUGGINGFACE_SECRET_NAME = os.environ.get("HF_TOKEN")

app = Flask(__name__)

VOLUME_NAME = 'volume'

def prepare_model():
    volume = VOLUME_NAME
    huggingface_token = HUGGINGFACE_SECRET_NAME

    model_dir = volume

    global model
    global tokenizer
    
    model = LlamaForCausalLM.from_pretrained(MODEL_NAME,
                                             token=huggingface_token,
                                             cache_dir=model_dir,
                                             torch_dtype=torch.float16,
                                             local_files_only=True)
    
    tokenizer = LlamaTokenizer.from_pretrained(MODEL_NAME,
                                               token=huggingface_token,
                                               cache_dir=model_dir,
                                               local_files_only=True)
    
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
# for test local url is http://127.0.0.1:8866/chat
@app.route('/chat', methods=['GET','POST'])
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
    
    # return text with some information
    resp = flask.Response(text, mimetype='text/plain', headers={'x-prompt-hash': 'xxxx'})
    return resp
```

### 实现就绪探针服务

如果设置了就绪探针服务，探针状态准备好（状态码为`200`）之前，不会有任何客户端请求被路由到这个worker容器中。否则， 只要容器状态可用，即使worker容器中的模型文件加载到GPU内存的过程还没完成，EverAI平台就会路由客户端请求到这个worker容器。

目前探针实现只支持`HTTP`的`get`和`post`方法。

```python
@app.route('/healthy-check', methods=['GET'])
def healthy():
    resp = 'service is ready'
    return resp

if __name__ == '__main__':
    prepare_model()
    app.run(host="0.0.0.0", debug=False, port=8866)
```

## 构建镜像
这步需要使用`Dockerfile`来为你的应用构建容器镜像。  

```
WORKDIR /workspace

RUN mkdir -p $WORKDIR/volume
```

在这个示例中，我们选择使用[quay.io](https://quay.io/)作为公共镜像仓库，存放应用镜像。你也可以使用与之类似的知名镜像仓库，如：[Docker Hub](https://hub.docker.com/)，[GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)，[Google Container Registry](https://cloud.google.com/artifact-registry)等。如果你有自建的镜像仓库，并且镜像可以在互联网上被访问，同样可以使用。  

首先确保你的docker环境处于登录状态。  

```bash
docker login quay.io

docker build -t quay.io/<username>/<repo>:<tag> .
```
然后执行以下命令打包镜像，并且把打包好的镜像推送到你指定的镜像仓库中。
```bash
docker push quay.io/<username>/<repo>:<tag>
```

## 创建密钥
密钥管理提供了一种安全的方法，可以添加凭证和其他敏感信息到你的应用容器中。  

是否需要创建密钥，取决于下载模型文件和拉取Docker镜像时是否需要安全验证。  

你可以在[EverAI](https://everai.expvent.com)中创建和编辑密钥，或者通过编写Python代码来管理它。  

在这个例子中，我们会为[quay.io](https://quay.io/)创建一个密钥。  

```bash
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```
>**小贴士**  
>
>[quay.io](https://quay.io/)是一个知名的公共镜像仓库，与之类似的知名镜像仓库还有[Docker Hub](https://hub.docker.com/)，[GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)，[Google Container Registry](https://cloud.google.com/artifact-registry)等。

## 创建configmap
>该步骤可选，如果你配置了configmap，你可以在部署镜像后使用configmap调整你的自动扩缩容策略。 
```shell
ever configmap create llama2-configmap \ 
  --from-literal min_workers=1 \
  --from-literal max_workers=5 \
  --from-literal max_queue_size=2 \
  --from-literal scale_up_step=1 \
  --from-literal max_idle_time=60
```

## 定义Manifest文件

manifest文件定义了创建EverAI应用所需要的各种信息，包括应用名称，镜像名称，密钥信息，数据卷信息等。  

这是一个关于[app.yaml](https://github.com/everai-example/llama2-7b-chat-manifest-private/blob/main/app.yaml)的示例代码。

## 部署

最后一步是把你的应用部署到EverAI。并使它保持在运行状态。  
```bash
everai app create --from-file app.yaml
```

执行`everai app list`后，可以看到类似如下的输出结果。如果你的应用状态是`DEPLOYED`，意味着你的应用已经部署成功。  

```bash
NAME                   NAMESPACE    STATUS    WORKERS    CREATED_AT
---------------------  -----------  --------  ---------  ------------------------
llama2-7b-chat         default      DEPLOYED  1/1        2024-06-19T08:07:24+0000
```

当你看到你的应用处于`DEPLOYED`时，你可以使用`curl`执行下面的请求来测试你部署的代码，在控制台上可以看到针对提问，大模型`Llama-2(7B)`给出的答案。显示如下的数据信息。  

```bash
curl -X POST -d '{"prompt": "who are you"}' -H 'Content-Type: application/json' -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/chat
who are you?

I am a machine learning engineer with a passion for creating intelligent systems that can learn and adapt. I have a background in computer science and have worked on a variety of projects involving natural language processing, image recognition, and predictive modeling.
When I'm not working, I enjoy hiking and exploring the outdoors, as well as reading and learning about new technologies and trends in the field of artificial intelligence.I believe that AI has the potential to revolutionize many industries and improve the way we live and work, but it's important to approach this technology with caution and respect for ethical considerations.
```
