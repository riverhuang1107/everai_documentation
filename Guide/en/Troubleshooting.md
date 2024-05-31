# Troubleshooting

## “everai: command not found” error
If you installed EverAI CLI but you’re seeing an error like `everai: command not found` when trying to run the CLI, this means that the installation location of Python package executables (“binaries”) are not present on your system path. This is a common problem; you need to reconfigure your system’s environment variables to fix it.  
* **Linux(WSL)**  
```bash
python3 -m site --user-base
```

We know that the scripts go to the `bin/` folder where the packages are installed. So we concatenate the paths with `bin/`. Finally, in order to avoid repeating the `export` command we add it to our `.bashrc` file and we run `source` to run the new changes.  
```bash
export PATH="/home/<username>/.local/bin:$PATH"
```
* **Windows PowerShell**  
```bash
python -m site --user-site
```

you can find the user base binary directory and replacing `site-packages` with `Scripts`. For example, this could return 			`C:\Users\<Username>\AppData\Roaming\Python\Python311\site-packages` so you would need to set your PATH to include `C:\Users\<Username>\AppData\Roaming\Python\Python311\Scripts`. You can set your user PATH permanently in the Control Panel. You may need to log out for the PATH changes to take effect.  

## Certificate issue with python 3.12
When you run command in EverAI CLI like `everai app list`, the output shows error like the following.
```bash
[SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1000)
```
This issue can pop up with macOS installations of Python. If you look in `Applications` > `Python 3.12` there's a script called `Install Certificates.command`. Running that often fixes this issue.

## “Route path exist” error
When you run command `everai app create`，the output shows the error like: `Route path exist`, you can change a app name, or set a new route name to solve this problem.
```bash
everai app create <your app name> --route-name <your app route name>
```
Globally unique route name. By default, it is same with the app name. Once the application name conflicts, route-name needs to be set explicitly.  

## "No app found in app.py" error
When running `everai app run`, an error message similar to `No app found in app.py` appears. You can use the `everai app check` command to further troubleshoot the problem. This command will display the specific problems encountered by your code. The output results are as follows:  

```bash
find object in app.py: 'Service' object has no attribute 'rout'
```

## Docker image build 401 UNAUTHORIZED error
When you run command everai image build，the output shows the error like: `401 UNAUTHORIZED\nERRoR: failed to solve: failed to push`, you should run command `docker login` to login the docker image repository, this example shows the command to login `quay.io`.  
```bash
docker login quay.io
```
## “Could not find a version that satisfies the requirement everai~=0.1.36” error
When you run command `everai image build`，the output shows the error like: `Could not find a version that satisfies the requirement everai~=0.1.36`. It means that the package version in `requirements.txt` does not exist when building the image. You can run the command `everai version` to get the current version, then update the `requirements.txt`. Or run the following command to generate `requirements` automatically.  

```bash
pip install pipreqs
pipreqs .
```


