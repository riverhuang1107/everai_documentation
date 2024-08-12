---
sidebar_position: 1
sidebar_label: everai cli changelog
---

# everai cli changelog

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