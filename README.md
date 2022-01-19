# that-gh-actions

Our shared GitHub actions repository

## Use

The GitHub workflow files here are shared amongst our api's for builds and deployments. 

GitHub documentation on reusing workflows: [https://docs.github.com/en/actions/using-workflows/reusing-workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

When switching over to these shared workflows, the name of the actions change and need to be updated in GitHub settings > Branches > Branch protection rules > edit > `Status checks that are required`.

For example, removing `build` and adding `Build Communications API / Build communications`. This name will depend on actual workflow names.

Here is an example "caller" workflow:

```yml
name: CI on Pull Request

on:
  pull_request:

jobs:
  build:
    name: Build Communications API
    uses: thatconference/that-gh-actions/.github/workflows/build-validate-api.yml@main
    with:
      apiName: communications
      isForDeploy: false
      branchName: ${{ github.ref_name }}
    secrets:
      SLACK_WEBHOOK_DEV_NOTIFICATIONS: ${{ secrets.SLACK_WEBHOOK_DEV_NOTIFICATIONS }}
```

```yml
name: CI on Push Master

on:
  push:
    branches:
      - main

jobs:
  build:
    name: Build Communications API for deploy
    uses: thatconference/that-gh-actions/.github/workflows/build-validate-api.yml@main
    with:
      apiName: communications
      isForDeploy: true
      branchName: ${{ github.ref_name }}
    secrets:
      SLACK_WEBHOOK_DEV_NOTIFICATIONS: ${{ secrets.SLACK_WEBHOOK_DEV_NOTIFICATIONS }}

  deploy:
    name: Deploy Communications API to Google Run
    needs: build
    uses: thatconference/that-gh-actions/.github/workflows/deploy-api.yml@main
    with:
      apiName: communications
      runMemory: 256Mi
    secrets:
      GCLOUD_AUTH: ${{ secrets.GCLOUD_AUTH }}
      GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}
      SLACK_WEBHOOK_DEV_NOTIFICATIONS: ${{ secrets.SLACK_WEBHOOK_DEV_NOTIFICATIONS }}

  refresh:
    name: Send refresh request to Gateway
    needs: [build,deploy]
    uses: thatconference/that-gh-actions/.github/workflows/refresh-gateway-schema.yml@main
    with:
      apiName: communications
```
