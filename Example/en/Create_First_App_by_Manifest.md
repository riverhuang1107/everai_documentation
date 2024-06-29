# Create first app by manifest yaml file
In the [quickstart](https://github.com/everai-example/get-start), we have seen how to create an EverAI application by creating an app object in [app.py](https://github.com/everai-example/get-start/blob/main/app.py). In addition to using the app object to create applications, we also provide a method to quickly create applications by defining a manifest yaml file. This method can deploy your application to the EverAI platform without importing the EverAI SDK code into your existing code.

## Create an app directory
### File and directory structure
Before creating app In [EverAI](https://everai.expvent.com), firstly you should create a directory which is named by your app name generally. A EverAI app includes the following four files at least:  

* **`Dockerfile`** This is the text file which is used to build docker image, and it includes series of instructions and commands used to build image.  

* **`app.py`** This is the code file which mainly provides your app's external service.  

* **`app.yaml`** This is the manifest file which mainly defines various information required to create an EverAI application, including application name, image name, key information, data volume information, etc.  

* **`requirements.txt`** This file defines the dependent packages and corresponding software versions for running Python application. You can use `pipreqs .` to obtain the latest software dependency packages and corresponding versions.

```bash
<your app name>
├── Dockerfile
├── app.py
├── app.yaml
├── requirements.txt
├── volume -> /root/.cache/everai/volumes/NYygLnXsD9sHYtn9C7y7hN
└── volume-test -> /root/.cache/everai/volumes/hupa49MesPXwmhFSy9Ku44
```

### Login EVERAI CLI
In your app directory, you should login by token you got in [EverAI](https://everai.expvent.com).  
   
```bash
everai login --token <your token>
```

## Write your app code in python
### Prepare volume
Before your application is deployed in the cloud, you should construct your volume first, if your app uses at least one volume.  

For production environment, the volumes are very important, you could call the following command to prepare it.  

```bash
everai volume create get-start-volume
```

You can get the local path of the volume `get-start-volume` through the `everai volume get` command. After entering the local path of the volume, you can store your files under this path. In the example code, the file `my-model` is stored in this path.

```bash
everai volume get get-start-volume
<Volume: id: NYygLnXsD9sHYtn9C7y7hN, name: get-start-volume, revision: 000001-2bd, files: 1, size: 11 B>
path: /root/.cache/everai/volumes/NYygLnXsD9sHYtn9C7y7hN
```

In the current application directory, create a soft link to mount the local path of the volume `get-start-volume`.  

```bash
ln -s /root/.cache/everai/volumes/NYygLnXsD9sHYtn9C7y7hN volume
```

In this example code, `my-model` file of volume `get-start-volume` in the local environment will be pushed to the cloud, when you run `everai volume push`.  

```bash
everai volume push get-start-volume
```

### Generate API endpoint service

There is an example code in [app.py](https://github.com/everai-example/get-start-manifest/blob/main/app.py).  

Now you can write your own Python code to implement the API endpoint service. The example here uses `flask` to implement an external service that reads the file `my-model` information in the volume `get-start-volume`.  

```python
import os
import time

import flask
from flask import Flask

app = Flask(__name__)

VOLUME_NAME = 'volume'
VOLUME_TEST_NAME = 'volume-test'
MODEL_FILE_NAME = 'my-model'

# curl http://localhost:8866/show-volume
@app.route('/show-volume', methods=['GET'])
def show_volume():
    model_path = os.path.join(VOLUME_NAME, MODEL_FILE_NAME)
    with open(model_path, 'r') as f:
        return f.read()

# curl http://localhost:8866/show-volume-test
@app.route('/show-volume-test', methods=['GET'])
def show_volume_test():
    model_path = os.path.join(VOLUME_TEST_NAME, MODEL_FILE_NAME)
    with open(model_path, 'r') as f:
        return f.read()
        
if __name__ == '__main__':
    app.run(host="0.0.0.0", debug=False, port=8866)
```

Now，you could test in your local machine will following command.  

```bash
python app.py
```
You can use `curl` to request this API endpoint service and see `hello world` displayed on your terminal.    

```bash
curl http://<your ip>:8866/show-volume
```

In addition, in the same application, you can implement multiple API endpoint services. This example uses `flask` to implement a external web service that sends active messages from the server to the client.  

```python
# https://everai.expvent.com/api/routes/v1/default/get-start-manifest/sse
# http://localhost:8866/sse
@app.route('/sse', methods=['GET'])
def sse():
    def generator():
        for i in range(10):
            yield f"hello again {i}"
            time.sleep(1)

    return flask.Response(generator(), mimetype='text/event-stream')
```

You can execute `python app.py` again, serving this web endpoint and hit it with `curl`, you will see the ten `SSE`(Server-Sent Events) events progressively appear in your terminal over a 10 second period.  

```bash
curl --no-buffer http://<your ip>:8866/sse
```

## Create secrets
Secrets are a secure way to add credentials and other sensitive information to the containers your functions run in.  

This step is optional, depending on whether the model and Docker image require security certification.  

You can create and edit secrets on [EverAI](https://everai.expvent.com), or programmatically from Python code.  

In this case, we will create one secret for [quay.io](https://quay.io/). 
```bash
everai secret create quay-secret \
  --from-literal username=<your username> \
  --from-literal password=<your password>
```
>**NOTE**
>
>[quay.io](https://quay.io/) is a well-known public image registry. Well-known image registry similar to [quay.io](https://quay.io/) include [Docker Hub](https://hub.docker.com/), [GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry), [Google Container Registry](https://cloud.google.com/artifact-registry), etc.  

## Build image
This step will build the container image using `Dockerfile`.    

```
WORKDIR /workspace

RUN mkdir -p $WORKDIR/volume
RUN mkdir -p $WORKDIR/volume-test
```

In this example, we choose to use [quay.io](https://quay.io/) as the public image registry to store application images. You can also use well-known image registry similar to [quay.io](https://quay.io/), such as [Docker Hub](https://hub.docker.com/), [GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry), [Google Container Registry](https://cloud.google.com/artifact-registry), etc. If you have a self-built image registry and the image can be accessed on the Internet, you can also use it.  

The dependence of this step is docker installed on your machine.  

```bash
docker login quay.io

docker build -t quay.io/<username>/<repo>:<tag> .
```
Then call the following command will compile the image and push them to your specified registry.  
```bash
docker push quay.io/<username>/<repo>:<tag>
```

## Define manifest file
The manifest file defines various information required to create an EverAI application, including application name, image name, key information, data volume information, etc.  

There is an example code in [app.yaml](https://github.com/everai-example/get-start-manifest/blob/main/app.yaml).  

```yaml
version: everai/v1alpha1
kind: App
metadata:
  name: get-start-manifest                          # application name
spec:
  image: quay.io/<username>/<repo>:<tag>       # image for serverless app
  imagePullSecrets:
    username:
      valueFrom:
        secretKeyRef:
          name: quay-secret
          key: username
    password:
      valueFrom:
        secretKeyRef:
          name: quay-secret
          key: password
  volumeMounts:
    - name: get-start-volume                                    # name
      mountPath: /workspace/volume       # mount path in container
      readOnly: true                              # only support `readOnly = true` currently, default is true
    - name: test-start-volume                                    # name
      mountPath: /workspace/volume-test       # mount path in container
      readOnly: true                              # only support `readOnly = true` currently, default is true

  port: 8866                                        # just one port cloud be set, everai will pass any http request /**
                                                  # to this port, default is 80
  readinessProbe:                                 # if readinessProbe is set up, there are no any request be route
                                                  # to this worker before probe status is ready ( status code = 200 ),
                                                  # otherwise (readinessProbe is not set up), everai will route reqeust
                                                  # to this worker when container is ready,
                                                  # even model not loaded into memory of gpu
    httpGet:                                      # http get and post probe is the only supported methods now
      path: /healthy-check                     # only http status 200 means ready

  volumes:                                        # optional field, but very important for AI app
    - name: get-start-volume                                    # volume name
      volume: 
        volume: get-start-volume          # use a private volume or a public volume from other user
    - name: test-start-volume                                    # volume name
      volume:
        volume: test-start-volume          # use a private volume or a public volume from other user
    - name: quay-secret
      secret:
        secretName: quay-secret

  resource:
    cpu: 1
    memory: 1 GiB
```

## Deploy image
The final step is to deploy your app to [EverAI](https://everai.expvent.com) and keep it running.
```bash
everai app create --from-file app.yaml
```
After running `everai app list`, you can see the result similar to the following. If your app's status is `DEPLOYED`, it means that your app is deployed successfully.   
```bash
NAME                             NAMESPACE    STATUS    WORKERS    CREATED_AT
-------------------------------  -----------  --------  ---------  ------------------------
get-start-manifest               default      DEPLOYED  1/1        2024-06-29T08:32:33+0000
```
Now, you can make a test call for your app, in these examples looks like:  
```bash
curl --no-buffer -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app route name>/sse
```