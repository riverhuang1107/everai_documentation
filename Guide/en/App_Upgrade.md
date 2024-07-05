# App Update
## update volume
If your app is deployed in [EverAI](https://everai.expvent.com) platform, when you want to update your files in volume, you can run `everai app update`. [EverAI](https://everai.expvent.com) platform support app hot upgrade,  and your application service will always be running online during the entire upgrade and update process.  

The following example is based on the app in the [quickstart](https://github.com/everai-example/get-start).

Run `everai worker list`, you can see a worker in running.  

```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
kEEBkaoEaZrxPgzab2ChjQ  RUNNING   FREE             2024-07-05T08:41:49+0000
```
Use `curl` to run the test case, the output of the example is on the terminal.  

```bash
curl -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/show-volume
hello world
hello world
```
Run `everai volume get get-start-volume`, you can find the path of the volume that is used in your app.  

```bash
<Volume: id: R9cFpk2JgmLcuiEfgAxPRd, name: get-start-volume, revision: 000001-08a, files: 1, size: 11 B>
path: /Users/<username>/.cache/everai/volumes/R9cFpk2JgmLcuiEfgAxPRd
```
Enter the directory of the volume,  and find the file named `my-model`, then update the file content. In this example, the new file content like this:   

```bash
hello world
hello world
hello world
hello world
```
Return to your app's directory, run `everai app prepare` to push the new file into the cloud.  

Run the following command to finish app update.  

```bash
everai app update
```
Run `everai worker list -a`, you can see the original worker is still running. And the new worker is deploying now.  

```bash
ID                      STATUS      DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  ----------  ---------------  ------------------------  ------------------------
kEEBkaoEaZrxPgzab2ChjQ  RUNNING     FREE             2024-07-05T08:41:49+0000
mNTAJoqyMRHDyDoLTCSjPn  CREATED     IN_FLIGHT        2024-07-05T13:11:59+0000
```
The original worker is terminated still the new worker is deployed. Run `everai worker list -a`, you can see is new worker is running.  

```bash
ID                      STATUS      DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  ----------  ---------------  ------------------------  ------------------------
kEEBkaoEaZrxPgzab2ChjQ  TERMINATED                   2024-07-05T08:41:49+0000  2024-07-05T13:12:23+0000
mNTAJoqyMRHDyDoLTCSjPn  RUNNING     FREE             2024-07-05T13:11:59+0000
```
Use `curl` to run the test case agian, the new output of the example is on the terminal now.  

```bash
curl -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/show-volume
hello world
hello world
hello world
hello world
```

## update image  
If your app is deployed in EverAI platform, when you want to update your code, you can run `everai app update`. [EverAI](https://everai.expvent.com) platform supports app hot upgrade,  and your application service will always be running online during the entire upgrade and update process.  

The following example is based on the app in the [quickstart](https://github.com/everai-example/get-start).  

Run `everai worker list`, you can see a worker in running.  

```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
PNLENbRP7AabNy6ZQWTogb  RUNNING   FREE             2024-07-05T06:45:19+0000
```

Use `curl` to run the test case, the output of the example is on the terminal. This example sends active messages from the server to the client.  

```bash
curl -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/sse
hello again 0

hello again 1

hello again 2
```
In this step, Open `app.py`, and change code from `hello again` to `hello world again`. Then you run `everai app run` to check your code locally.  

```python
import flask

@app.service.route('/sse', methods=['GET'])
def sse():
    def generator():
        for i in range(10):
            yield f"hello world again {i}\n\n"
            time.sleep(1)

    return flask.Response(generator(), mimetype='text/event-stream')
```

And then open `image_builder.py`, update your image registry's version. Run `everai image build` to buld image and push the image to [quay.io](https://quay.io/).   

```python
from everai.image import Builder

IMAGE = 'quay.io/<username>/<repo>:<tag>'
```
Aftering build image, you can run the following command to update your app.  

```bash
everai app update
```

Run `everai worker list -a`, you can see the new worker's detail status is `INITIALIZED`. 

```bash
ID                      STATUS       DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  -----------  ---------------  ------------------------  ------------
PNLENbRP7AabNy6ZQWTogb  RUNNING      FREE             2024-07-05T06:45:19+0000
kEEBkaoEaZrxPgzab2ChjQ  INITIALIZED  IN_FLIGHT        2024-07-05T08:41:49+0000
```

Until the new worker is in the `RUNNING` state, the original worker is already in the `TERMINATED` state.  

```bash
ID                      STATUS      DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  ----------  ---------------  ------------------------  ------------------------
PNLENbRP7AabNy6ZQWTogb  TERMINATED                   2024-07-05T06:45:19+0000  2024-07-05T08:42:28+0000
kEEBkaoEaZrxPgzab2ChjQ  RUNNING     FREE             2024-07-05T08:41:49+0000
```

Use `curl` to run the test case again, the new output of the example is on the terminal. This example sends new active messages from the server to the client.  

```bash
curl -H'Authorization: Bearer <your_token>' https://everai.expvent.com/api/routes/v1/<your namespace>/<your app name>/sse
hello world again 0


hello world again 1


hello world again 2


hello world again 3
```

## update resource

If your application has been deployed on the [EverAI](https://everai.expvent.com) platform cloud, you can use `everai app update` when you need to adjust your computing resources (CPU, memory, number of GPU cards or GPU model). The [EverAI](https://everai.expvent.com) platform supports hot application upgrades, and your application services are always running online during the entire upgrade and update process.

Open the `app.py` file, and in the code that creates and defines an app instance through the App class, modify the `resource_requests` parameter:  

```python
resource_requests=ResourceRequests(
    cpu_num=2,
    memory_mb=20480,
    gpu_num=1,
    gpu_constraints=[
        "A100 40G",
        "RTX 4090"
    ],
)
```

After saving and exiting the `app.py` file, execute the following command to complete the adjustment of application computing resources.  

```bash
everai app update
```

Run `everai worker list -a`, you can see the original worker's detail status is `TERMINATED`. And the new worker is running now.  

