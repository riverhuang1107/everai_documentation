# 应用端点认证

[EverAI](https://everai.expvent.com)平台支持3种应用端点服务的身份认证方式，分别是私密，公开和第三方认证。  

## 私密
当你通过[EverAI](https://everai.expvent.com)的CLI工具创建和部署应用后，应用的端点服务默认为通过私密认证方式访问。  

在访问应用的端点服务时，需要在请求端点服务时，加上自定义Header头`Authorization`，值为`Bearer <your_token>`。`<your_token>`使用从EverAI平台获取到的真实[令牌](https://everai.expvent.com/dashboard/token)替换。如果请求端点服务时，在Header头中不带上令牌信息，请求会失败。  

```bash
curl -X POST -d '{"prompt": "a photo of a cat on the building"}' -H 'Content-Type: application/json' -H'Authorization: Bearer <your_token>' -o test.png https://everai.expvent.com/api/routes/v1/stable-diffusion-v1-5/txt2img
```

## 公开

如果你的应用需要被公开访问，可以把你应用端点服务的认证方式修改为`公开`。你可以在EverAI平台的[应用](https://everai.expvent.com/dashboard/apps)中设置端点服务的认证方式。通过公开认证方式访问端点服务，在Header头中不需要带上令牌信息。  

```bash
curl -X POST -d '{"prompt": "a photo of a cat on the building"}' -H 'Content-Type: application/json' -o test.png https://everai.expvent.com/api/routes/v1/stable-diffusion-v1-5/txt2img
```
