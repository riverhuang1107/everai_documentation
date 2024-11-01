# everai app

## 显示worker容器列表

显示worker容器列表

```bash
https://everai.expvent.com/api/apps/v1/namespaces/{namespace}/apps/{name}/workers
```

### 请求参数

```bash
curl -X GET \
  -H 'Authorization: Bearer <your_token>' \
  https://everai.expvent.com/api/apps/v1/namespaces/<namespace>/apps/<name>/workers
```

|参数名称 |类型 |必填 |描述 |
|:-------------- |:--------------|:--------------|:--------------|
|`namespace` | string|是|命名空间名称，默认为`default`|
|`name` | string|是|应用名称|
|`showAll`   |boolean |否||
|`recentDays`  |integer |否|显示该应用最近天数内的所有worker容器，包括已经删除的和有问题的worker容器|

### 返回结果

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

```bash
curl -X POST \
  -H 'Authorization: Bearer <your_token>' \
  -d '{"scaleDownWorkersNum": "<scaleDownWorkersNum>", "workerIds": ["<workerIds>"]}' \
  https://everai.expvent.com/api/apps/v1/namespaces/<namespace>/apps/<name>/workers:scale-down
```

|参数名称 |类型 |必填 |描述 |
|:-------------- |:--------------|:--------------|:--------------|
|`namespace` | string|是|命名空间名称，默认为`default`|
|`name` | string|是|应用名称|
|`scaleDownWorkersNum`   |string |否||
|`workerIds`  |array[string] |是||

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

```bash
curl -X POST \
  -H 'Authorization: Bearer <your_token>' \
  -d '{"scaleUpWorkersNum": "<scaleUpWorkersNum>"}' \
  https://everai.expvent.com/api/apps/v1/namespaces/<namespace>/apps/<name>/workers:scale-up
```

|参数名称 |类型 |必填 |描述 |
|:-------------- |:--------------|:--------------|:--------------|
|`namespace` | string|是|命名空间名称，默认为`default`|
|`name` | string|是|应用名称|
|`scaleUpWorkersNum`   |string |否||

### 返回结果

```json
{
  "workerIds": [
    "5oSaHwT5Y9TmrocstmfWWj"
  ]
}
```