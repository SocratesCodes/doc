# SocratesCode Development Reference

This guide provides a comprehensive reference for developing SocratesCode, covering project structure, development workflow, testing strategies, and best practices.
## Development Workflow

### Getting Started
Follow these steps to set up your development environment.

!!! note "Branch to use"
    currently, the latest version is on the `demo` branch.

#### Prerequisites
- Git
- Python 3.11+
- Node.js 18+
- pipenv (After installed Python)
- [Podman](https://podman.io/) - for running MinIO locally. See [here](../additional setup/minio.md) for more details.

After install Python, you will need to install globally [pipenv](https://pipenv.pypa.io/en/latest/) for managing Python dependencies and environments.

Setup qdrant locally with the following guide: [here](../additional setup/qdrant.md) for more details.
For quick setup, below is the command to run qdrant locally:
```bash
podman run -p 6333:6333 qdrant/qdrant
```
Your server should be able to access qdrant at `http://localhost:6333`, and dashboard at `http://localhost:6333/dashboard`. You may need to change this in environment variables afterward to match the port and the qdrant url.

Setup MinIO with the following guide: [here](../additional setup/minio.md) for more details. For quick setup, below is the command to run MinIO locally:

!!! info "MinIO credentials"
    Change the `ROOTNAME` and `CHANGEME123` to your own and keep them somewhere safe as it is required for the next step.

!!! info "Folder for MinIO data"
    Create a folder for MinIO data. In this guide, we will use `~/minio/data` as the folder for MinIO data. (This will refers to your home directory. Create it if it doesn't exist.)

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

!!! info "For windows users"
    Use the following command instead (run via PowerShell):

```powershell
podman run `
   -p 9000:9000 `
   -p 9001:9001 `
   --name minio `
   -v ~/minio/data:/data `
   -e "MINIO_ROOT_USER=ROOTNAME" `
   -e "MINIO_ROOT_PASSWORD=CHANGEME123" `
   quay.io/minio/minio server /data --console-address ":9001"
```

Given you followed the exact command above, you should be able to access the MinIO console at `http://localhost:9001`. Login and create buckets for the project. One for storing team state and one for storing avatars image. Remember the names of the buckets as they will be used in the next step. For default one, you should create two buckets: `json-files` for storing team state and `avatars` for storing avatars image.

Then access the podman container to run commands:
```bash
podman exec -it minio bash
```

Authenticate the `mc` client:
```bash
mc alias set local http://127.0.0.1:9000 ROOTNAME CHANGEME123
```

Enable download policy for the avatars bucket. This is essential for the frontend to be able to download the avatars.

```bash
mc anonymous set download local/<YOUR_AVATAR_BUCKET>
```
where `<YOUR_AVATAR_BUCKET>` is the name of the avatars bucket you created. Default one is `avatars`.


### Set up environment variables
Set those environment variables as described in [Environment Variables](environment.md) based on the prerequisites you have created/installed.



#### Development Commands
View [setup](setup.md) for detailed guide on how to run our application locally.

1. In your desired folder, clone the repository:
```bash
git clone https://github.com/SocratesCodes/socratescode.git
git checkout demo # checkout to demo branch to get the latest version
```
2. Navigate to the project directory:
```bash
cd socratescode
```
Or open it in your favorite IDE then open the terminal to continue to the next step.

3. Install project dependencies and have pipenv environment created with dependencies installed:
```bash
npm install
```
   Our project is a monorepo, so doing this will install all dependencies for backend, frontend and AI.

4. Create an `.env` file in following places:
`/ai/` folder (for AI service), `/apps/backend/` folder (for backend service) and `/apps/frontend/` folder (for frontend service)
5. Generate PEM keys file with Openssl as described in [here](keys.md).
5. Setup necessary `.env` variables with [environments variable](environment.md) guides
6. Start the development server:
```bash
npm run dev
```

Visit http://localhost:3000 to access the frontend page.

### Development Environment URLs
Change those ports to 5001 if you are using macOS.
See [here](https://stackoverflow.com/questions/72369320/why-always-something-is-running-at-port-5000-on-my-mac) for more details.

- **Frontend**: http://localhost:3000
- **Backend API**: http://localhost:5000/api/health
- **API Documentation**: http://localhost:5000/api-docs
- **AI Service**: http://localhost:8002/health
