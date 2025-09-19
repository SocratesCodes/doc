# Environment configuration
!!! warning "Warning"
    Before filling in the environment variables, make sure you have created the `.env` file in the `/ai/`, `/apps/backend/` and `/apps/frontend/` folders and have setup project prerequisites as described in [Preparation](preparation.md).

!!! warning "Work in progress"
    This guide is still under development. Although at the moment the following guide should be enough to get you started, we will be updating it in the future as some of the features are still in development and these will be required to be configured.

!!! warning "for macos users"
    In these example we used port 5000 for backend service. This port is already in use by the macOS system. You will need to change the port to 5001. If you want to use this port, you may need to disable Airplay Receiver, a macOS service. See [here](https://stackoverflow.com/questions/72369320/why-always-something-is-running-at-port-5000-on-my-mac) for more details.
## AI service
These below configurations will be used in the `.env` file in the `/ai/` folder.

We used OpenAI API, as well as Mem0 for memory management. To configure those, fill in the following environment variables within the `.env` file in the `/ai/` folder:

```env
OPENAI_API_KEY=
MEM0_API_KEY=
```

For observation, we use Langfuse for tracing. Since it is cloud provider (in our case, but you can self-host it albeit we can't guarantee the stability of the service), its URL and API key are required:
```env
LANGFUSE_PUBLIC_KEY=
LANGFUSE_SECRET_KEY=
LANGFUSE_HOST=
```

With Autogen orchestrating the agents, we will need to fill the model name and its API key. This will be used to determine the model to use for the AI service configured via `model_config.yaml` file in the `/ai/` folder.

Additionally, tracing level will also need to be configured. We recommend using the provided setting for better observability.

```env
MODEL_NAME= # model name
MODEL_API_KEY= # model api key
AUTOGEN_DISABLE_RUNTIME_TRACING=true  # disable runtime tracing
```

!!! info "Regarding model that can be used"
    Currently we have only tested with OpenAI models. Other models supports are available with Autogen. Check out [here](https://microsoft.github.io/autogen/stable//user-guide/agentchat-user-guide/tutorial/models.html) for more details and see if your model is supported.

MinIO as file storage for storing team state. Below are the configurations we have used during development:
```env
JSON_BUCKET=json-files # json files bucket
USER_DATA_BUCKET=userdata # user data bucket
MINIO_DEFAULT_REGION=us-east-1 # default region
```

<!-- Qdrant: WIP -->



## Backend service

These below configurations will be used in the `.env` file in the `/apps/backend/` folder for running backend service.

**Server port** for running backend service - default is 5000:
```env
SERVER_PORT=5000 # server port
```

For persisting the **JWT key file**, the following configurations will be used for finding the defined path to make sure authentication and user related operations are working properly:
```env
JWT_PRIVATE_KEY_PATH= # private key path
JWT_PUBLIC_KEY_PATH= # public key path
```

!!! warning "Warning"
    Make sure the defined path for the JWT key file is correct and the file exists.
    
    On cloud service, you may want to keep those key files in docker volume instead of local file system. We have already defined the volume in the `docker-compose.yml` file, specifically `persistence` folder, so if you don't have any other preference, you can use that.

**Supabase** for handling database and authentication. Get your url and api key from your Supabase project and then fill in the following environment variables:
```env
SUPABASE_URL= # supabase url
SUPABASE_API_KEY= # supabase api key
```

**MinIO** for file storage:
```env
MINIO_ENDPOINT= # endpoint
MINIO_USE_SSL= # true or false - true if you are using SSL, false if you are not
MINIO_ACCESS_KEY= # access key
MINIO_SECRET_KEY= # secret key
MINIO_BUCKET= # avater bucket
MINIO_PORT= # port, default is 9000
```

You should have already setup the MinIO (see [here](../additional setup/minio.md) for more details). After that, you should set the following environment variables:

```env
MINIO_BUCKET= # avater bucket
JSON_BUCKET= # json files bucket
```

!!! info "For MinIO local development"
    We currently had only tested MinIO with version deployed on the cloud (see here). Hosting MinIO locally haven't tested and we can't guarantee it will work.

**OpenAI API key** for using OpenAI API-related features in our backend service:

```env
OPENAI_API_KEY=
```

Finally, `NODE_ENV` for determining the environment of the backend service. Since we are on local development, we will use `development`:
```env
NODE_ENV=development
```

## Frontend service
These below configurations will be used in the `.env` file in the `/apps/frontend/` folder for running frontend service.
```env
NEXT_PUBLIC_SUPABASE_URL= # supabase url
NEXT_PUBLIC_SUPABASE_ANON_KEY= # supabase anon key
```

You need to configure **Supabase** for some functions to work properly. Get your url and api key from your Supabase project and then fill in the following environment variables.

Then set the backend API URL. Since we are on local development, we will use `http://localhost:5000`.
```env
NEXT_PUBLIC_BACKEND_API_URL=http://localhost:5000
```