# zap-scan-api-action
This is a GitHub Action meant to be used as a [composite action](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action) within an existing workflow. This action encapsulates setting up a checkout and zap scan in one step. 

The action encapsulates the following other actions:

- [actions/checkout](https://github.com/actions/checkout)
- [zaproxy/action-api-scan](https://github.com/zaproxy/action-api-scan)


## Inputs

### `target`

**Required** target API definition, OpenAPI or SOAP, local file or URL, e.g. https://www.example.com/openapi.json
or target endpoint URL, GraphQL, e.g. https://www.example.com/graphql

### `rules_file_name`

**Required** You can also specify a relative path to the rules file to ignore any alerts from the ZAP scan. Make sure to create
the rules file inside the relevant repository. The following shows a sample rules file configuration.
Make sure to checkout the repository (actions/checkout@v2) to provide the ZAP rules to the scan action.

```tsv
10011	IGNORE	(Cookie Without Secure Flag)
10015	IGNORE	(Incomplete or No Cache-control and Pragma HTTP Header Set)
```

### `issue_title`

**Required** The title for the GitHub issue to be created.


### `fail_action`

**Required** By default ZAP Docker container will fail with an [exit code](https://github.com/zaproxy/zaproxy/blob/7abbd57f6894c2abf4f1ed00fb95e99c34ef2e28/docker/zap-api-scan.py#L35),
if it identifies any alerts. Set this option to `true` if you want to fail the status of the GitHub Scan if ZAP identifies any alerts during the scan.


## Usage
You can use this composite Action in your own workflow by adding:

```yml
name: OWASP (Zap Scan)

on:
  workflow_dispatch:
  schedule:
    - cron: "0 9,18 * * 1-5"

jobs:
  zap-scan-pre-prod-test:
    name: OWASP scan Pre-Prod Test
    runs-on: ubuntu-latest
    steps:
      - name: Checkout and Zap Scan
        uses: awazevr/zap-scan-api-action@v1.0.1
        with:
          target: 'https://xxxxxxxxxx.xxx.xxxxxxx/swagger.json'
          issue_title: 'Name of ZAP Scan Report'
          fail_action: 'true'
          rules_file_name: '.zap/rules.tsv' # << location of the rules file

```

