# everai app

## 显示worker容器列表

显示worker容器列表

```bash
https://everai.expvent.com/api/apps/v1/namespaces/{namespace}/apps/{name}/workers
```

### 请求参数

|参数名称 |类型 |描述 |
|:-------------- |:--------------|:--------------|
|`namespace` | string|命名空间名称，默认为`default`|
|`name` | string|应用名称|
|`showAll`   |boolean ||
|`recentDays`  |integer |显示最近天数内没有在运行中的worker容器|

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

#### Worker Status

|状态 |描述 |
|:-------------- |:--------------|
|`STATUS_UNSPECIFIED` | Worker容器异常 |
|`STATUS_INITIALIZED` |Worker容器初始化 |
|`STATUS_PENDING` |Worker容器挂起 |
|`STATUS_RUNNING` |Worker容器运行中 |
|`STATUS_TERMINATING` |Worker容器正在终止中 |
|`STATUS_ERROR` |Worker容器异常 |
|`STATUS_UNAVAILABLE` |Worker容器不可用|
|`STATUS_TERMINATED` |Worker容器已终止|
|`STATUS_CREATED` |Worker容器已创建 |

#### Worker Detail Status

|详细状态 |描述 |
|:-------------- |:--------------|
|`DETAIL_STATUS_UNSPECIFIED` |Worker容器异常|
|`DETAIL_STATUS_IN_FLIGHT` |Worker容器创建中|
|`DETAIL_STATUS_REMOVE` |Worker容器删除中|
|`DETAIL_STATUS_ROLLIN` |新的Worker容器替换旧的Worker容器|
|`DETAIL_STATUS_BUSY` |Worker容器忙碌中|
|`DETAIL_STATUS_FREE` |Worker容器空闲|

## 缩容worker容器

缩容worker容器

```bash
https://everai.expvent.com/api/apps/v1/namespaces/{namespace}/apps/{name}/workers:scale-down
```

### 请求参数

|参数名称 |类型 |描述 |
|:-------------- |:--------------|:--------------|
|`namespace` | string|命名空间名称，默认为`default`|
|`name` | string|应用名称|
|`scaleDownWorkersNum`   |string ||
|`workerIds`  |array[string] ||

### 返回结果

```json
{}
```

## 扩容worker容器

扩容worker容器

```bash
https://everai.expvent.com/api/apps/v1/namespaces/{namespace}/apps/{name}/workers:scale-up
```

### 请求参数

|参数名称 |类型 |描述 |
|:-------------- |:--------------|:--------------|
|`namespace` | string|命名空间名称，默认为`default`|
|`name` | string|应用名称|
|`scaleUpWorkersNum`   |string ||

### 返回结果

```json
{
  "workerIds": [
    "string"
  ]
}
```