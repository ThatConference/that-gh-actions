# that-gh-actions

Our shared GitHub actions repository

## Use

The GitHub workflow files here are shared amongst our api's for builds and deployments. 

GitHub documentation on reusing workflows: [https://docs.github.com/en/actions/using-workflows/reusing-workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

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
    secrets:
      SLACK_WEBHOOK_DEV_NOTIFICATIONS: ${{ secrets.SLACK_WEBHOOK_DEV_NOTIFICATIONS }}
```
