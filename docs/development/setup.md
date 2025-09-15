# Spin up local development environment

To set up your local development environment, follow these steps:

1. In your desired folder, clone the repository:
```bash
git clone https://github.com/SocratesCodes/socratescode.git
```

    ??? note "Validate the project structure"
        After cloning, ensure the project structure looks like this:
        ```
            socratescode/
            ├── ai/
            ├── apps/
            │   ├── backend/
            │   ├── docs/
            │   └── frontend/
            ├── .gitignore
            ├── LICENSE
            ├── README.md
            └── ...
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

5. Setup necessary `.env` variables with [environments variable](environment.md) guides
6. Start the development server:
```bash
npm run dev
```
    
	??? note "Running separate services"
        If you don't want to run all services concurrently, you can run each service separately by running the following commands:
        ```bash
        npm run dev:backend # run this only if you want to run backend service
        npm run dev:frontend # run this only if you want to run frontend service
        npm run dev:ai # run this only if you want to run AI service
        ```

This will start all server development servers concurrently.
!!! warning "Warning"
    Ensure you have set up all necessary environment variables in the `.env` files before starting the development server to avoid any possible bugs that stem from misconfiguration.

Now that you have set up the development environment and run the whole application stack, you can begin using SocratesCode for testing, development or simply just use it. To validate the development environment, you can visit localhost:3000 to access the frontend page. In the next guide we will discuss how to use/validate SocratesCode functionality working correctly.
