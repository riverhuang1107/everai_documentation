# App Upgrade
## everai app upgrade --volume-requests
If your app is deployed in [EverAI](https://everai.expvent.com) platform, when you want to update your files in volume, you can run `everai app upgrade --volume-requests`. [EverAI](https://everai.expvent.com) platform support app hot upgrade,  and your application service will always be running online during the entire upgrade and update process.  

Run `everai worker list`, you can see a worker in running.  


```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
bqZJ4eTjMmEbP9ncrvPgGg  RUNNING   FREE             2024-05-11 19:24:08+0800
```
Use `curl` to run the test case, the output of the example is on the terminal.  

```bash
curl -H'Authorization: Bearer everai_637wE9obZtmGLyqIJp0lok' https://everai.expvent.com/api/apps/v1/routes/test-start-4/show-volume
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

Run the following command to finish app upgrade.  

```bash
everai app upgrade --volume-requests
```
Run `everai worker list`, you can see the older worker is stiall running. And the new worker is deploying now.  

```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
bqZJ4eTjMmEbP9ncrvPgGg  RUNNING   FREE             2024-05-11 19:24:08+0800
GtxtbdHn2rFEkZqZxSesyE  RUNNING   NSPECIFIED       2024-05-12 18:20:38+0800
```
The older worker is removed still the new worker is deployed. Run `everai worker list`, you can see is new worker is running.  

```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
GtxtbdHn2rFEkZqZxSesyE  RUNNING   FREE             2024-05-12 18:20:38+0800
```
Use `curl` to run the test case agian, the new output of the example is on the terminal now.  

```bash
curl -H'Authorization: Bearer everai_637wE9obZtmGLyqIJp0lok' https://everai.expvent.com/api/apps/v1/routes/test-start-4/show-volume
hello world
hello world
hello world
hello world
```

## everai app upgrade --image  
If your app is deployed in EverAI platform, when you want to update your code, you can run `everai app upgrade --image`. [EverAI](https://everai.expvent.com) platform support app hot upgrade,  and your application service will always be running online during the entire upgrade and update process.  

Run `everai worker list`, you can see a worker in running.  

```bash
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
dEXndYfRrpqwAirhBdugYN  RUNNING   FREE             2024-05-11 14:54:27+0800
```
Use `curl` to run the test case, the output of the example is on the terminal. This example sends active messages from the server to the client.  

```bash
curl -H'Authorization: Bearer everai_637wE9obZtmGLyqIJp0lok' https://everai.expvent.com/api/apps/v1/routes/test-start-5/sse
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

And then open `image_builder.py`, update your image registry's version. Run `everai image build` to buld image and push the image to quay.io.   

```bash
from everai.image import Builder

IMAGE = 'quay.io/<username>/<repo>:<tag>'
```
Aftering build image, you can run the following command to upgrade your app.  

```bash
everai app upgrade --image
```
Run `everai app list`, now you can see the app status is `PREPARING`. 
```bash
NAME          STATUS     CREATED_AT                ROUTE_NAME
------------  ---------  ------------------------  ------------
test-start-5  PREPARING  2024-05-11 14:54:25+0800  test-start-5
```
Run `everai worker list`, you can see the older worker's detail status is `REMOVE`. And the new worker is running now.  

```bash
hhq@HHQ:~/app/test-start$ everai worker list
ID                      STATUS    DETAIL_STATUS    CREATED_AT                DELETED_AT
----------------------  --------  ---------------  ------------------------  ------------
dEXndYfRrpqwAirhBdugYN  RUNNING   REMOVE           2024-05-11 14:54:27+0800
SeaNG9f6hKcQ9J3X93GQEx  RUNNING   FREE             2024-05-11 15:11:37+0800
```
Use `curl` to run the test case again, the new output of the example is on the terminal. This example sends  new active messages from the server to the client.  

```bash
curl -H'Authorization: Bearer everai_637wE9obZtmGLyqIJp0lok' https://everai.expvent.com.cn:1111/api/apps/v1/routes/test-start-5/sse
hello world again 0


hello world again 1


hello world again 2


hello world again 3
```

