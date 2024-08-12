# everai cli changelog

## 0.2.31 (2024-08-06)

* Applications can configure multiple ports for externally exposed services.  

```yaml
...
spec:
  ...
  services:
  - port: 8188
  - port: 18188
  ...
``