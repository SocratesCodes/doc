# Prerequisites

This guide assumes you have already met the minimum
[requirements](requirements.md) and have your server ready. 

The following guide was written based on our team experience with RACE Hub (powered by AWS).

## Deployment

- Docker
- Docker Compose
- [Coolify](https://coolify.io/) (view [here](https://coolify.io/self-hosted/) for one-command setup)


Optionally you may also want to have an own domain name and a DNS provider if
your platform doesn't support open custom ports (This can be workaround via VPC
into your server, but it is required if you want to use Coolify's GitHub
integration). Afterward, you should setup server URL to your own domain name if you want Coolify to accessible via your own domain name (To do this, look for "Settings" at the bottom left of the Coolify sidebar). Exercise caution when doing this with firewalls and other security measures.

!!! warning "Warning"
    Our app is designed with Coolify in mind, meaning that our docker compose file will need some adjustments to work with plain Docker deployment or other deployment platforms.