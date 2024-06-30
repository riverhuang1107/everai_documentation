# App Endpoint Authentication

[EverAI](https://everai.expvent.com) platform supports three identity authentication methods for application endpoint services, namely private, public and third-party authentication.

## Private

When you create and deploy an application through the CLI tool of [EverAI](https://everai.expvent.com), the application's endpoint service is accessed through private authentication by default.  

When accessing the application's endpoint service, you need to add a custom header `Authorization` with a value of `Bearer <your_token>` when requesting the endpoint service. Replace `<your_token>` with the real [Token](https://everai.expvent.com/dashboard/token) obtained from the EverAI platform. If the token information is not included in the header when requesting the endpoint service, the request will fail.

```bash
curl -X POST -d '{"prompt": "a photo of a cat on the building"}' -H 'Content-Type: application/json' -H'Authorization: Bearer <your_token>' -o test.png https://everai.expvent.com/api/routes/v1/default/stable-diffusion-v1-5/txt2img
```

## Public

If your application needs to be publicly accessible, you can change the authentication method of your application endpoint service to `public`. You can set the authentication method of the endpoint service in the [Apps](https://everai.expvent.com/dashboard/apps) of the EverAI platform. Accessing the endpoint service through public authentication does not require token information in the header.

```bash
curl -X POST -d '{"prompt": "a photo of a cat on the building"}' -H 'Content-Type: application/json' -o test.png https://everai.expvent.com/api/routes/v1/default/stable-diffusion-v1-5/txt2img
```
