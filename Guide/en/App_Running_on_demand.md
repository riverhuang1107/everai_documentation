# App Running on-demand
In [EverAI](https://everai.expvent.com) platform, you always pay for what you use and nothing more. You never pay for idle resources — just actual compute time. This goal can be achieved through the reasonable use of `everai app pause` and `everai app resume`.  


Run the following command to stop app of the example. Pause an app, all worker will be stopped.  

```bash
everai app pause <your app name>
```
Run `everai worker list <your app name>`, and show the work list again, you can see nothing in worker list.  

```bash
ID    STATUS    DETAIL_STATUS    CREATED_AT    DELETED_AT
----  --------  ---------------  ------------  ------------
```
Run command `everai app list`, you can see that  the status of app is `PAUSED`. Note that `CREATED_AT` uses UTC time display.   

```bash
NAME                             NAMESPACE    STATUS    WORKERS    CREATED_AT
-------------------------------  -----------  --------  ---------  ------------------------
test-start-004                   default      PAUSED    0/0        2024-06-28T05:27:24+0000
```
After the worker is stopped, your application's billing will also be stopped. Based on your actual business needs, when you deploy the application again, the application will continue to be billed by the system.  

```bash
 everai app resume <your app name>
```
After running `everai app list`, you can see your app's status.  If status is `DEPLOYED`, it means that your app is deployed once again successfully.  

```bash
NAME                             NAMESPACE    STATUS    WORKERS    CREATED_AT
-------------------------------  -----------  --------  ---------  ------------------------
test-start-004                   default      DEPLOYED  1/1        2024-06-28T05:27:24+0000
```


