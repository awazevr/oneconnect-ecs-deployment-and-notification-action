# oneconnect-ecs-deployment-and-notification-action

This is a GitHub Action meant to be used as a [composite action](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action) within an existing workflow. This action encapsulates setting up a checkout, Amazon ECS deployment, deployment notification to Newrelic and Microsoft teams in one step.

The action encapsulates the following other actions:

- [actions/checkout](https://github.com/actions/checkout)
- [aws-actions/configure-aws-credentials](https://github.com/aws-actions/configure-aws-credentials)
- [hashicorp/setup-terraform](https://github.com/hashicorp/setup-terraform)
- [aws-actions/amazon-ecs-deploy-task-definition](https://github.com/aws-actions/amazon-ecs-deploy-task-definition)
- [newrelic/deployment-marker-action](https://github.com/newrelic/deployment-marker-action)
- [jdcargile/ms-teams-notification](https://github.com/jdcargile/ms-teams-notification)

## Inputs

### `aws-region`

**Required** Aws region

### `aws-account-id`

**Required** Aws account id

### `terraform-working-directory`

**Required** Working directory of your terraform modules

### `terraform-state-bucket`

**Required** Amazon s3 bucket name where the terraform state is maintained

### `terraform-state-key`

**Required** Terraform state key name

### `image-tag`

**Required** Task definition file docker image tag

### `variable-file`

**Required** Variable file path

### `task-definition-file`

**Required** Task definition file name

### `service-name`

**Required** Name of ECS service

### `cluster-name`

**Required** Name of ECS cluster

### `wait-for-service-stability`

**Required** Flag to wait for ECS service stability after deployment (true/false)

### `is-codedeploy`

**Optional** Flag to identify whether the ECS deployment is a code deploy or not (true/false), default is false

### `codedeploy-appspec`

**Optional** Codedeploy appspec file name and it's required if 'is-codedeploy' is set to true

### `codedeploy-application`

**Optional** Codedeploy applicaion name and it's required if 'is-codedeploy' is set to true

### `codedeploy-deployment-group`

**Optional** Codedeploy group name and it's required if 'is-codedeploy' is set to true

### `notify-newrelic`

**Optional** Flag to add deployment marker in New Relic or not (true/false), default is false

### `newrelic-api-key`

**Optional** New Relic api key and it's required if 'notify-newrelic' is set to true

### `newrelic-account-id`

**Optional** New Relic account id and it's required if 'notify-newrelic' is set to true  

### `newrelic-application-id`

**Optional** New Relic application id and it's required if 'notify-newrelic' is set to true  

### `notify-ms-team`

**Optional** Flag to notify Microsoft team or not after deployment (true/false), default is false

### `ms-teams-webhhok-uri`

**Optional** Microsoft teams webhook URI and is required if 'notify-ms-team' is set to true

### `ms-teams-notification-summary`

**Optional** Microsoft teams notification summary and is required if 'notify-ms-team' is set to true

### `ms-teams-notification-summary`

**Optional** GitHub token and is required if 'notify-ms-team' is set to true


## Usage
You can use this composite Action in your own workflow by adding:

```yml
name: Deploy Pre-Prod
    if: ${{ github.actor != 'dependabot[bot]' && github.ref == 'refs/heads/master'}}
    needs: build
    runs-on: ubuntu-latest
    environment: preprod
    permissions:
      id-token: write
      contents: write
      security-events: write
      pull-requests: write
      actions: read
    steps:
      - name:  Deploy to Preprod
        uses: awazevr/oneconnect-ecs-deployment-and-notification-action@v1.0.0
        with:
          aws-region: ${{ secrets.AWS_REGION }}
          aws-account-id: ${{ secrets.AWS_ACCOUNT_ID }}
          terraform-working-directory: "Terraform"
          terraform-state-bucket: "terraform-state-bucket-name"
          terraform-state-key: "terraform-state-key"
          image-tag: ${{ github.sha }}
          variable-file: "vars/preprod.tfvars"
          task-definition-file: "Terraform/task-definition.json"
          service-name: "Name of your service"
          cluster-name: "Cluster name"
          wait-for-service-stability: false
          is-codedeploy: true
          codedeploy-appspec: "appspec.yaml"
          codedeploy-application: "code deploy application name"
          codedeploy-deployment-group: "codedeploy deployment group name"
          notify-newrelic: true
          newrelic-api-key: ${{ secrets.NEW_RELIC_API_KEY }}
          newrelic-account-id: ${{ secrets.NEW_RELIC_ACCOUNT_ID }}
          newrelic-application-id: ${{ secrets.NEW_RELIC_APPLICATION_ID }}
          notify-ms-team: true
          ms-teams-webhhok-uri: ${{ secrets.TEAMS_WEBHOOK_URI }}
          ms-teams-notification-summary: "Deployed ${{ github.sha }} to Pre-Prod"
          github-token: ${{ github.token }}

```

