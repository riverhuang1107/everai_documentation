# everai app

## 显示worker容器列表

显示worker容器列表

```api
https://everai.expvent.com/api/apps/v1/namespaces/{namespace}/apps/{name}/workers
```

### 请求参数

|字段名称 |类型 |描述 |
|:-------------- |:--------------|:--------------|
|namespace | string|命名空间名称，默认为`default`|
|name | string|应用名称|
|showAll   |boolean ||
|recentDays  |integer |显示最近天数内没有在运行中的worker容器|

### 返回结果

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

## 缩容worker容器

缩容worker容器

```api
https://everai.expvent.com/api/apps/v1/namespaces/{namespace}/apps/{name}/workers:scale-down
```

### 请求参数

|字段名称 |类型 |描述 |
|:-------------- |:--------------|:--------------|
|namespace | string|命名空间名称，默认为`default`|
|name | string|应用名称|
|scaleDownWorkersNum   |string ||
|workerIds  |array[string] ||

### 返回结果

```json
{}
```

## 扩容worker容器

扩容worker容器

```api
https://everai.expvent.com/api/apps/v1/namespaces/{namespace}/apps/{name}/workers:scale-up
```

### 请求参数

|字段名称 |类型 |描述 |
|:-------------- |:--------------|:--------------|
|namespace | string|命名空间名称，默认为`default`|
|name | string|应用名称|
|scaleUpWorkersNum   |string ||

### 返回结果

```json
{
  "workerIds": [
    "string"
  ]
}
```