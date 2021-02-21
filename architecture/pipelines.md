# Pipelines

## Deploying to prod
- it is dangerous to have a pipeline deploy to prod directly because it means your CI system has prod keys
- it is preferrable to have the CI system publish a new container or artifact which a separate system deploys
  - the separate system can be configured to deploy the new container or artifact via polling or web hook

## Packaging your app
- dont package your app with the creds it needs to run because it likely has to sit in a container registry somewhere and is now tied to the environment it lives in
- packaging config with app because that means you have to redeploy everytime you change config or secrets (ex: if your current secrets are compromised)