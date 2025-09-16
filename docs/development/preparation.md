# Prepare your local development environment

To prepare your local development environment, you will need to install the following prerequisites and configure the following cloud services.

!!! note "Note"
    Some of these services will be required to be configured. See [Environment Variables](environment.md) for more details.

## Prerequisites

You will need the following tools installed on your local development machine:

- Git
- Python 3.11+
- Node.js 18+
- pipenv (After installed Python)

After install Python, you will need to install globally [pipenv](https://pipenv.pypa.io/en/latest/) for managing Python dependencies and environments.

## Cloud services

Beside local, there are several cloud services that you will need to configure to run SocratesCode:

- **OpenAI API Key**: You will need an OpenAI API key to use the AI features of SocratesCode. You can get an API key by signing up for an account on the [OpenAI website](https://platform.openai.com/signup).
- **Supabase**: You will need a Supabase account to use the database and authentication
- **Mem0**: A Mem0 account is required to use the memory features of SocratesCode. You can sign up for a free account on the [Mem0 website](https://www.mem0.ai/).
- **Langfuse**: A Langfuse account is required to use the tracing features of SocratesCode. You can sign up for a free account on the [Langfuse website](https://langfuse.com/).

You may need to deploy some of the following services to your own server (an option to local development is possible, however we haven't tested it yet and can't guarantee it will work). See [Cloud development](../deployment/intro.md) for more detail:

- **MinIO**: File storage, s3-compatible object storage which is used for storing team state and files.