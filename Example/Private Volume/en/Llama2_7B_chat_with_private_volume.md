# Llama-2(7B) Chat with private volume
In the quickstart, you created a simple application. In this example, we use a private volume to store the `Llama-2(7B)` model files and implement an AIGC(AI generated content) online question and answer service.  

## Login EVERAI CLI
Create a directory for your app firstly. In your app directory, you should login by token you got in [EverAI](https://everai.expvent.com).  

```bash
everai login --token <your token>
```

## Create secrets
This step is optional, if you have already created a secret for the registry you need to access, or if your registry is publicly accessible.  

In this case, we will create one secret for [quay.io](https://quay.io/).  

```bash
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```
>**NOTE**
>
>[quay.io](https://quay.io/) is a well-known public image registry. Well-known image registry similar to [quay.io](https://quay.io/) include [Docker Hub](https://hub.docker.com/), [GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry), [Google Container Registry](https://cloud.google.com/artifact-registry), etc.  

You should create a secret to access [Hugging Face](https://huggingface.co/) as well.
```bash
everai secret create your-huggingface-secret-name \
  --from-literal token-key-as-your-wish=<your huggingface token>
```

## Write your app code in python
### Basic setup
There is an example code in [app.py](https://github.com/everai-example/llama2-7b-chat-with-private-volume/blob/main/app.py).  

First, import the required EverAI Python class library. Then define the variable names that need to be used, including the volume, the secret that accesses the image registry, and the file stored in the volume. Use the `Image.from_registry` static method to create a image instance. Create and define an app instance through the App class.  

What needs to be noted here is that you need to configure GPU resources for your application. The GPU model configured here is "A100 40G", and the number of GPU cards is 1.  

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

### Load model
  
If your local environment does not have a model file, you can use the `LlamaForCausalLM.from_pretrained` method to pass in `MODEL_NAME` to pull the model file from the [Hugging Face](https://huggingface.co/) official website. And by setting `cache_dir`, the model file will be cached in the private volume `models--meta-llama--llama-2-7b-chat-hf`.  

You can get the local path of the volume `models--meta-llama--llama-2-7b-chat-hf` through the `everai volume get` command. After entering the local path of the volume, you can see the model files that have been cached.  

```bash
everai volume get models--meta-llama--llama-2-7b-chat-hf
<Volume: id: UoswTkyjkPZU3qb27s4B9E, name: models--meta-llama--llama-2-7b-chat-hf, revision: 000001-821, files: 21, size: 25.11 GiB>
path: /root/.cache/everai/volumes/UoswTkyjkPZU3qb27s4B9E
```
When using `everai app run` to debug the sample code in the local environment, the value of `is_prepare_mode` is `False`, and the operation of pushing local files to the cloud will not be performed. If your local debugging environment has GPU resources, the system will successfully execute `model.cuda(0)`.  

After your code is debugged, execute the `everai app prepare` command. This command will execute all methods annotated by `@app.prepare`. At this time, the value of `is_prepare_mode` is `True`. In the sample code, the model files of volume `models--meta-llama--llama-2-7b-chat-hf` in the local environment will be pushed to the cloud when this command is executed.

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

### Generate inference service
Aftering loading `Llama-2(7B)` model, now you can write your Python code that uses `flask` to implement the inference online service of AIGC(AI generated content).  

```python
import flask
import typing

tokenizer: typing.Optional[PreTrainedTokenizerBase] = None
model = None

# service entrypoint
# api service url looks https://everai.expvent.com/api/routes/v1/default/llama2-7b-chat/chat
# for test local url is http://127.0.0.1:80/chat
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
## Build image
This step will build the container image, using two very simple files `Dockerfile` and `image_builder.py`.  

There is an example code in [image_builder.py](https://github.com/everai-example/llama2-7b-chat-with-private-volume/blob/main/image_builder.py).  

In `image_builder.py`, you should set your image repo.  

In this example, we choose to use [quay.io](https://quay.io/) as the public image registry to store application images. You can also use well-known image registry similar to [quay.io](https://quay.io/), such as [Docker Hub](https://hub.docker.com/), [GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry), [Google Container Registry](https://cloud.google.com/artifact-registry), etc. If you have a self-built image registry and the image can be accessed on the Internet, you can also use it.  

```python
from everai.image import Builder

IMAGE = 'quay.io/<username>/<repo>:<tag>'
```
The dependence of this step is docker and buildx installed on your machine. Otherwise we will have further prompts to help you install them.  

```bash
docker login quay.io
docker buildx version
```
Then call the following command will compile the image and push them to your specified registry.  

```bash
everai image build
```

## Deploy image
The final step is to deploy your app to everai and keep it running.  

```bash
everai app create
```

After running `everai app list`, you can see the result similar to the following. If your app's status is `DEPLOYED`, it means that your app is deployed successfully.  

```bash
NAME                   NAMESPACE    STATUS    WORKERS    CREATED_AT
---------------------  -----------  --------  ---------  ------------------------
llama2-7b-chat         default      DEPLOYED  1/1        2024-06-19T08:07:24+0000
```

When your app is deployed, you can use `curl` to execute the following request to test your deployed code, and you can see that `Llama-2(7B)` model gives the answers to the question on the console. The following data information is displayed.  

```bash
curl -X POST -d '{"prompt": "who are you"}' -H 'Content-Type: application/json' -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/chat
who are you?

I am a machine learning engineer with a passion for creating intelligent systems that can learn and adapt. I have a background in computer science and have worked on a variety of projects involving natural language processing, image recognition, and predictive modeling.
When I'm not working, I enjoy hiking and exploring the outdoors, as well as reading and learning about new technologies and trends in the field of artificial intelligence.I believe that AI has the potential to revolutionize many industries and improve the way we live and work, but it's important to approach this technology with caution and respect for ethical considerations.
```
