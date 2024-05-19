# everai overview

EverAI CLI命令行用于管理你的EverAI应用和其他资产  

**示例**:  


构建应用镜像过程中，在终端显示详细的执行日志信息。  

```bash
everai -v image build
```

**格式**:   
```bash
everai [-h] [-v]  {login,logout,worker,workers,w,config,image,images,i,run,app,apps,a,secret,secrets,sec,s,volume,volumes,vol,autoscaling,configmap,cm,configmaps,version}...
```

**命令**:
* `login`               登录，如果你没有token，请访问[EverAI](everai.expvent.com)  
* `logout`              登出
* `worker (workers, w)` 管理应用的worker
* `config`              显示当前的环境变量配置信息
* `image (images, i)`   镜像管理
* `run`                 在本地环境运行应用进行测试
* `app (apps, a)`       应用管理
* `secret (secrets, sec, s)`  管理密钥
* `volume (volumes, vol)`  管理存储对象
* `configmap (cm, configmaps)`  ConfigMap管理
* `version`             显示EverAI CLI命令行工具和客户端的软件版本

**选项**:  
 * `-h, --help`         显示帮助信息
 * `-v, --verbose`      输出详细的日志信息
 

