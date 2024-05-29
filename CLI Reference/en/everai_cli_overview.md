---
sidebar_position: 2
sidebar_label: everai cli overview
---

# everai cli overview
EverAI Client for managing your EverAI application and other asserts  


**Example**:  

During the process of building an application image, detailed execution log information is displayed on the terminal.  


```bash
everai -v image build
```

**Usage**:   
```bash
everai [-h] [-v]  {login,logout,worker,workers,w,config,image,images,i,run,app,apps,a,secret,secrets,sec,s,volume,volumes,vol,autoscaling,configmap,cm,configmaps,version} ...
```

**Commands**:
* `login`               Login, if you do not have a token, please visit [EverAI](https://everai.expvent.com)   
* `logout`              Logout 
* `worker (workers, w)` Manage the worker of app 
* `config`              Print current configuration 
* `image (images, i)`   Image management 
* `run`                 Local run a everai application for test 
* `app (apps, a)`       Manage app 
* `secret (secrets, sec, s)`  Manage secrets 
* `volume (volumes, vol)`  Manage volume 
* `configmap (cm, configmaps)`  Manage configmaps 
* `version`             Show package and client version 

**Options**:  
 * `-h, --help`          Show this help message and exit 
 * `-v, --verbose`       Verbose output 
 

