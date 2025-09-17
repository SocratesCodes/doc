# Deploy to your server

To start, you will need to have your server ready with
[necessary software](preparation.md) and make sure it meets the
[requirements](requirements.md).

We recommend using official Coolify guide aside our guide to help you deploy the application and learn more about our deployment platform. See [here](https://coolify.io/docs/knowledge-base/docker/compose) or their [official documentation](https://coolify.io/docs/get-started/introduction) for more details.

1. Setup [GitHub integration on Coolify](https://coolify.io/docs/knowledge-base/git/github/integration)
2. Create a new resource on Coolify and select the repository and branch you want to deploy

Then on the creation screen:

- Select the branch you want to deploy
- Configure docker compose file location in the repository
- Go to next step
- (Optional) Configure domain name
- Configure [environment variables](environment.md) in the "Environment Variables" section of the newly created resource page.
- Click on "Deploy"
- Wait for the deployment to finish
- Access the application at the URL provided by Coolify

!!! warning "Warning"
    The first time you deploy this app, you may need to create the JWT key files in the `/persistence` folder. See [here](environment.md) for more details.
