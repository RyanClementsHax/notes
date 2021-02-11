# Pipelines

## Deploying to prod
- it is dangerous to have a pipeline deploy to prod directly because it means your CI system has prod keys
- it is preferrable to have the CI system publish a new container or artifact which a separate system deploys
  - the separate system can be configured to deploy the new container or artifact via polling or web hook