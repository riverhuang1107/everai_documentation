---
sidebar_position: 1
sidebar_label: everai cli changelog
---

# everai cli changelog

## 0.2.33 (2024-10-10)

## 0.2.32 (2024-10-08)

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