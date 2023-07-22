# deploy-bento-action
A GitHub Action to deploy bento to cloud

## Example Usage

```yaml
name: Deploy Bento
on:
  push:
    tags:
      - v*

jobs:
  build_and_deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - uses: actions/setup-python@v4
        with:
          python-version: '3.10'
          cache: 'pip'

      - name: Build and Deploy
        uses: bentoml/deploy-bento-action
        with:
          cloud_api_token: ${{ secrets.CLOUD_API_TOKEN }}
          cloud_endpoint: ${{ secrets.CLOUD_ENDPOINT }}
```

## Action Inputs

| Name                | Description                                                         | Required | Default           |
| ------------------- | ------------------------------------------------------------------- | -------- | ----------------- |
| `cloud_api_token`   | An API token generated from the cloud console                       | Yes      | N/A               |
| `cloud_endpoint`    | The endpoint URL of Yatai or BentoCloud                             | Yes      | N/A               |
| `bentoml_version`   | The version of bentoml to use                                       | No       | latest            |
| `bento_version`     | The version of Bento Build or leave it empty to use a random string | No       | Generated         |
| `deployment_config` | The path to the JSON deployment config                              | No       | `deployment.json` |
| `deployment_name`   | The name of the deployment, required if action is 'update'          | No       | N/A               |
| `action`            | Choose the action for the deployment: 'create', 'update', 'skip'    | No       | 'update'          |

## Action Outputs

| Name               | Description                                                                     |
| ------------------ | ------------------------------------------------------------------------------- |
| `bento_repository` | The name of the Bento repository                                                |
| `bento_version`    | The version of the built Bento                                                  |
| `bento_tag`        | The tag of the built Bento, in the format of `$bento_repository:$bento_version` |

An example to use the outputs from this action:

```yaml
jobs:
  build_and_deploy:
    runs-on: ubuntu-latest
    steps:
      # ...
      - name: Build and Deploy
        id: deploy-bento   # give it an id
        uses: bentoml/deploy-bento-action
        with:
          cloud_api_token: ${{ secrets.CLOUD_API_TOKEN }}
          cloud_endpoint: ${{ secrets.CLOUD_ENDPOINT }}

      - name: Display Bento Info
        run: |  # use the id to access the outputs
          echo "Bento Repository: ${{ steps.deploy-bento.outputs.bento_repository }}"
          echo "Bento Version: ${{ steps.deploy-bento.outputs.bento_version }}"
          echo "Bento Tag: ${{ steps.deploy-bento.outputs.bento_tag }}"
```

## License

[Apache License 2.0]([LICENSE](https://github.com/bentoml/deploy-bento-action/blob/main/LICENSE))
