# EverAI Chat with Llama-2(7B)
In the quickstart, you created a simple application. In this example, we use `Llama-2(7B)` to implement a  AIGC(AI generated content) online question and answer service.  

## Create an app
Create a directory for your app firstly. In your app directory, you should login by token you got in [EverAI](https://everai.expvent.com). After login successfully, run command `everai app create` to create your app.  

```bash
everai login --token <your token>

everai app create <your app name>
```

## Create secrets
  
In this case, we will create one secret for [quay.io](https://quay.io/).  

```bash
everai secret create your-quay-io-secret-name \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```

## Write your app code in python
### Basic setup
There is an example code in app.py.  

First, import the required EverAI Python class library. Then define the variable names that need to be used, including the volume, the secret that accesses the image registry, and the file stored in the volume. Use the Image.from_registry static method to create a image instance. Create and define an app instance through the App class.  

What needs to be noted here is that you need to configure GPU resources for your application. The GPU model configured here is "A100 40G", and the number of GPU cards is 1.  

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

### Load model
Before your application is deployed in the cloud, you should construct your volume first, if your app uses at least one volume.  

You can create a function by the `@app.prepare` decorator to manager and prepare your volume and related files.  

```bash
everai volume pull expvent/llama2-7b-chat
```
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

### Generate inference service
Aftering loading `Llama-2(7B)` model, now you can write your Python code that uses `flask` to implement the inference online service of AIGC(AI generated content).  

```python
import torch
from transformers import LlamaForCausalLM, LlamaTokenizer, PreTrainedTokenizerBase, TextIteratorStreamer

import flask
import typing

tokenizer: typing.Optional[PreTrainedTokenizerBase] = None
model = None

# service entrypoint
# api service url looks https://everai.expvent.com/api/apps/v1/routes/llama2-7b-chat-example/chat
# for test local url is http://127.0.0.1:5050/chat
@app.service.route('/chat', methods=['POST'])
def chat():
    #assert pipeline is not None

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
    # context.Done()
    return resp
```
## Build image
This step will build the container image, using two very simple files `Dockerfile` and `image_builder.py`.  

In `image_builder.py`, you should set your image repo.  

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
everai app deploy
```

After running `everai app list`, you can see the result similar to the following. If your app's status is `DEPLOYED`, it means that your app is deployed successfully.  


```bash
NAME                         STATUS     CREATED_AT                ROUTE_NAME
---------------------------  ---------  ------------------------  ---------------------------
test-llama2-7b-chat-modal    DEPLOYED   2024-05-15 10:23:53+0800  test-llama2-7b-chat-modal
```
Now, you can make a test call for your app, in these examples looks like:  

```bash
curl -X POST -d '{"prompt": "who are you"}' -H 'Content-Type: application/json' -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/apps/v1/routes/<your app route name>/chat
```




