---
sidebar_position: 1
sidebar_label: everai cli changelog
---

# everai cli changelog

## 0.2.34 (2024-10-10)

* Applications support controlling the expansion and contraction of worker containers through [API](https://expvent.com/documentation/docs/API%20Reference/everai_app_api). The yaml file configuration example in manifest mode is as follows:

```yaml
...
spec:
  ...
  autoscaler:
    manual:
      defaultWorkerNum: 1
  ...
```

## 0.2.33 (2024-10-10)

```bash
[2024-10-09 17:46:51][WARNING][:781] Retrying (Retry(total=2, connect=None, read=None, redirect=None, status=None)) after conection broken by 'SSLError(SSLCertVerificationErrOr(1, '[SSL: CERTIFICATE_VERIFY FAILED] certificate verify failed: unable to get local issuer certificate(_ssl.c:1007)'))':/api/volumes/v1/volumes
```

## 0.2.31 (2024-08-06)

* Applications can configure multiple ports for externally exposed services. An example of yaml file configuration in manifest mode is as follows:    

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

* When pushing volume to the EverAI platform，the terminal can display the files pushing process, and the output results are as follows:

```bash
Uploading ...ots/462165984030d82259a11f4367a4eed129e94a7b/model_index.json: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 609/609 [00:00<00:00, 1799.51B/s]
Uploading ...984030d82259a11f4367a4eed129e94a7b/text_encoder_2/config.json: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 575/575 [00:00<00:00, 2007.34B/s]
Uploading                                            files:   5%|███████▊                                                                                                                                        | 2/37 [00:01<00:21,  1.64file/s]
Uploading ...9a11f4367a4eed129e94a7b/text_encoder_2/model.fp16.safetensors:   0%|                                                                                                                                    | 0/1325.01 [00:00<?, ?MiB/s]
```