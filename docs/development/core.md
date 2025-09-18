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
- [Podman](https://podman.io/) - for running MinIO locally

After install Python, you will need to install globally [pipenv](https://pipenv.pypa.io/en/latest/) for managing Python dependencies and environments.


### Set up environment variables
Set those environment variables as described in [Environment Variables](environment.md) based on the prerequisites you have created/installed.


#### Development Commands
View [setup](setup.md) for detailed guide on how to run our application locally.

### Development Environment URLs
- **Frontend**: http://localhost:3000
- **Backend API**: http://localhost:5000/api/health
- **API Documentation**: http://localhost:5000/api-docs
- **AI Service**: http://localhost:8002/health
