# everai app

## List workers

List workers

```bash
https://everai.expvent.com/api/apps/v1/namespaces/{namespace}/apps/{name}/workers
```

### Request

```bash
curl -X GET \
  -H 'Authorization: Bearer <your_token>' \
  https://everai.expvent.com/api/apps/v1/namespaces/<namespace>/apps/<name>/workers
```

|Field Name |Type |Required |Description |
|:-------------- |:--------------|:--------------|:--------------|
|`namespace` | string| Yes | namespace name, Defaults to `defualt`|
|`name` | string| Yes |app name|
|`showAll`   |boolean | No ||
|`recentDays`  |integer | No |display the workers that have existed in the between before day and now, default 2 day, value unit is day|

### Response

Here’s what the response payload looks like as JSON:

```json
{
  "workers": [
    {
      "id": "JYdCWQfbKLAhEf8WnZUY34",
      "deviceId": "95fa81d0-c46c-4d77-9194-20c65eb34733",
      "status": "STATUS_RUNNING",
      "detailStatus": "DETAIL_STATUS_FREE",
      "createdAt": "2024-09-29T09:35:18.362Z",
      "launchAt": "2024-09-29T09:35:23.443Z",
      "lastServeAt": "2024-09-29T09:36:02.218108Z",
      "successCount": 1,
      "CPUs": 2,
      "memory": 1024,
      "appId": "nmusD8nfVdsdc8q3cAmcbE"
    },
    {
      "id": "LZeebuHERVEcWxfaw3z7vJ",
      "deviceId": "c2f3a824-0b33-4f14-a4c1-a4e417c90755",
      "status": "STATUS_RUNNING",
      "detailStatus": "DETAIL_STATUS_FREE",
      "createdAt": "2024-09-29T09:35:18.370Z",
      "launchAt": "2024-09-29T09:35:23.547Z",
      "lastServeAt": "2024-09-29T09:35:35.387883192Z",
      "CPUs": 2,
      "memory": 1024,
      "appId": "nmusD8nfVdsdc8q3cAmcbE"
    }
  ]
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

```bash
curl -X POST \
  -H 'Authorization: Bearer <your_token>' \
  -d '{"scaleDownWorkersNum": "<scaleDownWorkersNum>", "workerIds": ["<workerIds>"]}' \
  https://everai.expvent.com/api/apps/v1/namespaces/<namespace>/apps/<name>/workers:scale-down
```

|Field Name |Type |Required |Description |
|:-------------- |:--------------|:--------------|:--------------|
|`namespace` | string|Yes | namespace name, Defaults to `defualt`|
|`name` | string|Yes |app name|
|`scaleDownWorkersNum`   |string |No ||
|`workerIds`  |array[string] |No ||

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

```bash
curl -X POST \
  -H 'Authorization: Bearer <your_token>' \
  -d '{"scaleUpWorkersNum": "<scaleUpWorkersNum>"}' \
  https://everai.expvent.com/api/apps/v1/namespaces/<namespace>/apps/<name>/workers:scale-up
```

|Field Name |Type |Required |Description |
|:-------------- |:--------------|:--------------|:--------------|
|`namespace` | string|Yes | namespace name, Defaults to `defualt`|
|`name` | string|Yes |app name|
|`scaleUpWorkersNum` |string |No ||

### Response

Here’s what the response payload looks like as JSON:

```json
{
  "workerIds": [
    "string"
  ]
}
```