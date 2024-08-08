# EverAI公开数据卷模型文件资源

* **Meta Llama**
  
|Volume Name |Model|
|:-------------- |:--------------|
|everai/models--meta-llama--llama-2-7b-chat-hf| meta-llama/Llama-2-7b-chat-hf|                    
|everai/models--meta-llama--meta-llama-3-8b  |meta-llama/Meta-Llama-3-8B |

* **Stable Diffusion**

|Volume Name |Model|
|:-------------- |:--------------|
|everai/models--runwayml--stable-diffusion-v1-5 | runwayml/stable-diffusion-v1-5 |
|everai/models--stabilityai--stable-diffusion-2-1| stabilityai/stable-diffusion-2-1 |
|everai/models--stabilityai--stable-diffusion-xl-base-1-0        |stabilityai/stable-diffusion-xl-base-1.0|
|everai/models--stabilityai--stable-diffusion-3-medium-diffusers|stabilityai/stable-diffusion-3-medium-diffusers|

如果你想在本地使用`everai app run`调试这个示例，你的本地调试环境需要有GPU资源（英伟达GPU或者具有Metal编程框架的苹果GPU），并且在调试代码前使用`everai volume pull`命令把云端的模型文件拉取到本地环境。  

```bash
everai volume pull everai/models--runwayml--stable-diffusion-v1-5
```
