---
sidebar_position: 1
sidebar_label: everai cli changelog
---

# everai cli changelog

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
``