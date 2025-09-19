# Storing application files with MinIO

MinIO is a distributed object storage system that is compatible with the S3 API. In our project, it is used to store team state and files. This step should be done during the [development](../development/environment.md) or [deployment](../deployment/environment.md) setup.

By the end of this guide, you should be able to:

- Setup MinIO locally with Podman
- Setup MinIO on your own server with Coolify.

## Setup MinIO locally
!!! info "Prerequisites"
    Ensure you have installed [Podman](https://podman.io/) on the server. See [here](../development/preparation.md) for more details.

1. Run the following command to initialize the MinIO container (change the `ROOTNAME` and `CHANGEME123` to your own and remember them as it is required for the next step):
```bash
podman run \
   -p 9000:9000 \
   -p 9001:9001 \
   --name minio \
   -v ~/minio/data:/data \
   -e "MINIO_ROOT_USER=ROOTNAME" \
   -e "MINIO_ROOT_PASSWORD=CHANGEME123" \
   quay.io/minio/minio server /data --console-address ":9001"
```

    ??? info "For windows users"
        You may need to run the command in PowerShell. It will be a little bit different from the command above since PowerShell uses different syntax for newlines:

        ```powershell
        podman run `
          -p 9000:9000 `
          -p 9001:9001 `
          --name minio `
          -v "$HOME/minio/data:/data" `
          -e "MINIO_ROOT_USER=ROOTNAME" `
          -e "MINIO_ROOT_PASSWORD=CHANGEME123" `
          quay.io/minio/minio server /data --console-address ":9001"
        ```

        Ensure you are in home directory when running the command.

    The following steps will refer `MINIO_ROOT_USER` and `MINIO_ROOT_PASSWORD` as `ROOTNAME` and `CHANGEME123` respectively.

2. Once running, you should be able to access the MinIO console at `http://localhost:9001`. Login and create buckets for the project. One for storing team state and one for storing avatars image.

    In our guide, we will refer to these buckets as `json-files` and `userdata` respectively.

3. Set those in your application's [environment variables](../development/environment.md):

    - `MINIO_BUCKET` - bucket for storing user avatars.
    - `JSON_BUCKET` - bucket for storing json files.
I

4. Access the podman container to run commands:
```bash
podman exec -it minio bash
```

5. Authenticate the `mc` client:
```bash
mc alias set local http://127.0.0.1:9000 ROOTNAME CHANGEME123
```
where ROOTNAME and CHANGEME123 are the same as the ones you used to initialize the MinIO container.

6. Enable download policy for the avatars bucket. This is essential for the frontend to be able to download the avatars.
```bash
mc anonymous set download local/<YOUR_AVATAR_BUCKET>
```
where `<YOUR_AVATAR_BUCKET>` is the name of the avatars bucket you created.

At this point, you should be able to change profile picture, as well as AI should work properly. Additional configurations may needed (see [here](../development/environment.md) and [preparation](../deployment/preparation.md) for more details).

## Setup MinIO on your own server
!!! info "Prerequisites"
    Ensure you have already met the minimum [requirements](../deployment/requirements.md) and have your server ready. See [here](../deployment/preparation.md) for more details.

1. Setup [Coolify](https://coolify.io/) on your server if you haven't already.
2. Create a new resource on Coolify and select MinIO.
3. In MinIO docker compose file, edit its file to below:
```yaml
services:
  minio:
    image: 'quay.io/minio/minio:latest'
    command: 'server /data --console-address ":9001"'
    environment:
      # - MINIO_SERVER_URL=$MINIO_SERVER_URL
      # - MINIO_BROWSER_REDIRECT_URL=$MINIO_BROWSER_REDIRECT_URL
      - MINIO_ROOT_USER=$SERVICE_USER_MINIO
      - MINIO_ROOT_PASSWORD=$SERVICE_PASSWORD_MINIO
    volumes:
      - 'minio-data:/data'
    healthcheck:
      test:
        - CMD
        - mc
        - ready
        - local
      interval: 5s
      timeout: 20s
      retries: 10
```
The main difference compared to the Coolify's template is that both `MINIO_SERVER_URL` and `MINIO_BROWSER_REDIRECT_URL` are not needed.

4. Configure Service Specific Configurations to match your preferences.

    !!! warning "Warning"
        per [this](https://coolify.io/docs/services/minio), you need to run MinIO on https since it don't support http authentication.

        Also for URL, you may need a domain to access the MinIO console.

5. Deploy the resource.
6. Access the MinIO console url, authenticate and then perform step 2 of the local setup above.
7. Set MinIO configured credentials in your application's [environment variables](../development/environment.md#backend-service).
8. On Coolify, access the resource container's terminal and perform step 5 and 6 of the local setup above.

## What's next?
Check other [development prerequisites guides](../development/preparation.md) and [deployment prerequisites guides](../deployment/deployment.md) for other requirements if unmet.
