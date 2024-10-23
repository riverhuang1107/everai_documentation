---
sidebar_position: 1
sidebar_label: everai cli changelog
---

# everai cli changelog

## 0.2.37 (2024-10-22)

* The `--update` parameter is added to the `everai volume push` command to perform data volume push operations when the local source file exists or the cloud target file does not exist, or the size of the local source file is inconsistent with the cloud target file.
  
## 0.2.35 (2024-10-11)

* Optimized the timeout reconnection logic of `everai volume push`.

## 0.2.33 (2024-10-10)

* Fixed the issue where when executing the `everai volume list` command, the following error may occur, that is, the local CA certificate cannot be obtained.

```bash
[2024-10-09 17:46:51][WARNING][:781] Retrying (Retry(total=2, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError(SSLCertVerificationError(1, '[SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate(_ssl.c:1007)'))': /api/volumes/v1/volumes
```

## 0.2.32 (2024-10-08)

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