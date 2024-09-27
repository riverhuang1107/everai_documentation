# everai app

## List workers

List workers

```bash
https://everai.expvent.com/api/apps/v1/namespaces/{namespace}/apps/{name}/workers
```

### Request

|Field Name |Type |Description |
|:-------------- |:--------------|:--------------|
|`namespace` | string|namespace name, Defaults to `defualt`|
|`name` | string|app name|
|`showAll`   |boolean ||
|`recentDays`  |integer |display the workers that have existed in the between before day and now, default 2 day, value unit is day|

### Response

Here’s what the response payload looks like as JSON:

```json
{
  "workers": [
    {
      "id": "string",
      "deviceId": "string",
      "status": "STATUS_UNSPECIFIED",
      "detailStatus": "DETAIL_STATUS_UNSPECIFIED",
      "createdAt": "2024-09-26T12:33:22.807Z",
      "deletedAt": "2024-09-26T12:33:22.807Z",
      "launchAt": "2024-09-26T12:33:22.807Z",
      "lastServeAt": "2024-09-26T12:33:22.807Z",
      "successCount": 0,
      "failedCount": 0,
      "sessionNumber": 0,
      "CPUs": 0,
      "memory": 0,
      "GPUName": "string",
      "GPUs": 0,
      "appId": "string",
      "appRevision": "string",
      "replaceWorkerId": "string",
      "userId": "string",
      "requestId": "string",
      "gpuModel": "string",
      "gpuNum": 0,
      "stopAt": "string",
      "devicePublicAddress": "string"
    }
  ],
  "queueSize": 0,
  "queueName": "string",
  "lastScaleUpTime": "2024-09-26T12:33:22.807Z",
  "lastScaleDownDeleteTime": "2024-09-26T12:33:22.807Z",
  "maxWorkers": 0
}
```

#### Worker Status

|Status |Description |
|:-------------- |:--------------|
|`STATUS_UNSPECIFIED` |The worker is unspecified. |
|`STATUS_INITIALIZED` |The worker is initialized. |
|`STATUS_PENDING` |The worker is pending. |
|`STATUS_RUNNING` |The worker is running. |
|`STATUS_TERMINATING` |The worker is terminating. |
|`STATUS_ERROR` |The worker is error. |
|`STATUS_UNAVAILABLE` |The worker is not available.|
|`STATUS_TERMINATED` |The worker is terminated.|
|`STATUS_CREATED` |The worker is created. |

#### Worker Detail Status

|Detail Status |Description |
|:-------------- |:--------------|
|`DETAIL_STATUS_UNSPECIFIED` |The worker is unspecified.|
|`DETAIL_STATUS_IN_FLIGHT` |The worker is creating.|
|`DETAIL_STATUS_REMOVE` |The worker is removing.|
|`DETAIL_STATUS_ROLLIN` |The new worker is rolling in.|
|`DETAIL_STATUS_BUSY` |The worker is busy.|
|`DETAIL_STATUS_FREE` |The worker is free.|

## Scale down workers

Scale down workers

```bash
https://everai.expvent.com/api/apps/v1/namespaces/{namespace}/apps/{name}/workers:scale-down
```

### Request

|Field Name |Type |Description |
|:-------------- |:--------------|:--------------|
|`namespace` | string|namespace name, Defaults to `defualt`|
|`name` | string|app name|
|`scaleDownWorkersNum`   |string ||
|`workerIds`  |array[string] ||

### Response

Here’s what the response payload looks like as JSON:

```json
{}
```

## Scale up workers

Scale up workers

```bash
https://everai.expvent.com/api/apps/v1/namespaces/{namespace}/apps/{name}/workers:scale-up
```

### Request

|Field Name |Type |Description |
|:-------------- |:--------------|:--------------|
|`namespace` | string|namespace name, Defaults to `defualt`|
|`name` | string|app name|
|`scaleUpWorkersNum` |string ||

### Response

Here’s what the response payload looks like as JSON:

```json
{
  "workerIds": [
    "string"
  ]
}
```