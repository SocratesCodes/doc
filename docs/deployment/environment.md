# Deployment environment variables

This guide assumes you have already met the minimum [requirements](requirements.md) and have your server ready.

## Environment variables
These are set in the Environment Variables section of the Coolify app. Make sure you have configured the project beforehand based on [this guide from Coolify](https://coolify.io/docs/knowledge-base/git/github/integration).

Most of the environment variables are similar to [development environment variables](development/environment.md).

However, there are some differences:

`NODE_ENV` for determining the environment of the backend service. Since we are on deployment, we will use `production`:
```env
NODE_ENV=production
```

`JWT_PRIVATE_KEY_PATH` and `JWT_PUBLIC_KEY_PATH` for the path to the JWT key files. Given that you are using our default `docker-compose.yml` file, we had these keys stored in the `/persistence` folder. Note that those file **MUST** be present in the `/persistence` folder at the time of deployment. You may need to upload them to the server, have them placed in the `/persistence` folder, and redeploy the application (You only need to do this once):
```env
JWT_PRIVATE_KEY_PATH=./persistence/private_key.pem
JWT_PUBLIC_KEY_PATH=./persistence/public_key.pem
```

Compared to local development, change those environment variables to the following:
```env
BACKEND_API_URL=http://backend_service:5000
BACKEND_ORIGIN=http://backend_service:5000
BACKEND_URL=http://backend_service:5000
EXPOSED_ORIGIN=http://ai_service:8002
```
Change the service url to the actual service url to connect to the backend service, since we are using Docker:
```env
NEXT_PUBLIC_AI_SERVICE_URL=http://ai_service:8002
NEXT_PUBLIC_BACKEND_API_URL=http://backend_service:5000
```

and also change the websocket url to the actual websocket url to connect to the backend service:
```env
WEBSOCKET_URL=http://backend_service:5000
```