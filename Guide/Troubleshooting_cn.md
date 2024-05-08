# Troubleshooting

## “everai: command not found” error
如果你安装了EverAI CLI工具，但你在执行命令时发现了类似`everai: command not found`这样的错误，这意味着你的系统路径中还没有设置可以执行Python可执行包（二进制可执行包）的系统路径。这是一个常见问题；你需要通过配置你的系统环境变量来修复它。  
* **Linux(WSL)**  
```bash
python3 -m site --user-base
```

执行上述命令获得目录，如`/home/<username>/.local/`，Python的二进制包文件安装在该目录的`bin/`下。我们可以把两者进行组合，得到路径`/home/<username>/.local/bin`。为了避免每次使用时设置该环境变量，我们可以把该命令加入到`.bashrc`，并且执行`source`命令，使之立即生效。  
```bash
export PATH="/home/hhq/.local/bin:$PATH"
```
* **Windows PowerShell**  
```bash
python -m site --user-site
```

执行上述命令，你可以得到存放Python二进制包的基础路径，然后把路径中的`site-packages`替换成`Scripts`。举例来说，如果命令返回的是`C:\Users\<Username>\AppData\Roaming\Python\Python311\site-packages`，那么你需要在你的环境变量中添加`C:\Users\<Username>\AppData\Roaming\Python\Python311\Scripts`。你可以在系统属性的环境变量中，添加这条路径。最后你需要关闭`PowerShell`窗口，重新打开后使之生效。

## Certificate issue with python 3.12
当你在执行EverAI CLI的命令如`everai app list`时，出现类似如下的报错信息。  

```bash
[SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1000)
```
出现这个问题的原因是你的本地环境是macOS，并且使用了dmg软件包安装了Python环境。如果你在`应用程序`目录下存在`Python 3.12`文件夹，文件夹中有一个名为`Install Certificates.command`的脚本文件，运行安装这个文件后，再次执行EverAI CLI的命令，上述问题可以解决。

## Docker image build 401 UNAUTHORIZED error
执行`everai image build`命令时，如果出现类似`401 UNAUTHORIZED\nERRoR: failed to solve: failed to push`的错误提示，你需要执行`docker login`命令登录到docker镜像仓库，这里的示例是登录到`quay.io`镜像仓库。  

```bash
docker login quay.io
```



