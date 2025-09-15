# Deployment

To start, you will need to have your server ready with
[necessary software](preparation.md) and make sure it meets the
[requirements](requirements.md).

1. Setup [github integration on Coolify](https://coolify.io/docs/knowledge-base/git/github/integration)
2. Create a new resource on Coolify and select the repository and branch you want to deploy

Then on the creation screen:
- Select the branch you want to deploy
- Configure docker compose file location in the repository
- Go to next step
- (Optional) Configure domain name
- Configure [environment variables](environment.md)
- Click on "Deploy"
- Wait for the deployment to finish
- Access the application at the URL provided by Coolify

!!! warning "Warning"
    The first time you deploy this app, you may need to create the JWT key files in the `/persistence` folder. See [here](environment.md) for more details.
