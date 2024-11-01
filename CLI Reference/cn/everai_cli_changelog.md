---
sidebar_position: 1
sidebar_label: everai cli changelog
---

# everai cli changelog

## 0.2.38 (2024-11-01)

* 应用支持`combinedVolume`特性。
 
## 0.2.37 (2024-10-22)

* `everai volume push`命令新增`--update`参数，针对本地源文件存在且云端目标文件不存在，或本地源文件的大小与云端目标文件不一致的情况，进行数据卷推送操作。
  
## 0.2.35 (2024-10-11)

* 优化了`everai volume push`时，超时重连的逻辑。
  
## 0.2.33 (2024-10-10)

* 修复了在执行`everai volume list`指令时，有概率出现如下所示报错，即获取不到本地CA证书的问题。

```bash
[2024-10-09 17:46:51][WARNING][:781] Retrying (Retry(total=2, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError(SSLCertVerificationError(1, '[SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate(_ssl.c:1007)'))': /api/volumes/v1/volumes
```

## 0.2.32 (2024-10-08)

* 应用支持通过[API](https://expvent.com/documentation/zh-cn/docs/API%20Reference/everai_app_api)来控制worker容器的扩缩容，manifest模式下yaml文件配置示例如下：
  
```yaml
...
spec:
  ...
  autoscaler:
    manual:
      defaultWorkerNum: 1
  ...
```

## 0.2.31 (2024-08-06)

* 应用可以配置多个对外暴露服务的端口。manifest模式下yaml文件配置示例如下：  

```yaml
...
spec:
  ...
  services:
  - port: 8188
  - port: 18188
  ...
```

## 0.2.26 (2024-07-23)

* 当数据卷被推送到EverAI平台时，终端可以显示文件推送进度，如下所示：

```bash
Uploading ...ots/462165984030d82259a11f4367a4eed129e94a7b/model_index.json: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 609/609 [00:00<00:00, 1799.51B/s]
Uploading ...984030d82259a11f4367a4eed129e94a7b/text_encoder_2/config.json: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 575/575 [00:00<00:00, 2007.34B/s]
Uploading                                            files:   5%|███████▊                                                                                                                                        | 2/37 [00:01<00:21,  1.64file/s]
Uploading ...9a11f4367a4eed129e94a7b/text_encoder_2/model.fp16.safetensors:   0%|                                                                                                                                    | 0/1325.01 [00:00<?, ?MiB/s]
```