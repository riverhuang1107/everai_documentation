# everai app

## List workers

List workers

```api
/api/apps/v1/namespaces/{namespace}/apps/{name}/workers
```

### Request

|Field Name |Type |Description |
|:-------------- |:--------------|:--------------|
|namespace | string|namespace|
|name | string|app name|
|showAll   |boolean ||
|recentDays  |integer |display the workers that have existed in the between before day and now, default 2 day, value unit is day|

### Response

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

## Scale down workers

Scale down workers

```api
/api/apps/v1/namespaces/{namespace}/apps/{name}/workers:scale-down
```

### Request

|Field Name |Type |Description |
|:-------------- |:--------------|:--------------|
|namespace | string|namespace|
|name | string|app name|
|showAll   |boolean ||
|recentDays  |integer |display the workers that have existed in the between before day and now, default 2 day, value unit is day|

### Response

```json
{}
```

## Scale up workers

Scale up workers

```api
/api/apps/v1/namespaces/{namespace}/apps/{name}/workers:scale-up
```

### Request

|Field Name |Type |Description |
|:-------------- |:--------------|:--------------|
|namespace | string|namespace|
|name | string|app name|
|showAll   |boolean ||
|recentDays  |integer |display the workers that have existed in the between before day and now, default 2 day, value unit is day|

### Response

```json
{
  "workerIds": [
    "string"
  ]
}
```