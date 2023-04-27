<img src="https://avatars.githubusercontent.com/u/40179672" width="75">

[![hack.d Lawrence McDaniel](https://img.shields.io/badge/hack.d-Lawrence%20McDaniel-orange.svg)](https://lawrencemcdaniel.com)
[![discuss.overhang.io](https://img.shields.io/static/v1?logo=discourse&label=Forums&style=flat-square&color=ff0080&message=discuss.overhang.io)](https://discuss.overhang.io)
[![docs.tutor.overhang.io](https://img.shields.io/static/v1?logo=readthedocs&label=Documentation&style=flat-square&color=blue&message=docs.tutor.overhang.io)](https://docs.tutor.overhang.io)<br/>
[![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)
[![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)

# tutor-k8s-get-secret

Github Action to securely extract and decrypt secrets data from k8s and transpose into environment variables that can be consumed by tutor with a ´tutor config save´ operation. a k8s secret of the form:

```bash
  MYSQL_HOST: your-rds-instance.xyzw93ffci2g.us-east-2.rds.amazonaws.com
  MYSQL_PORT: "3306"
  NOTES_MYSQL_PASSWORD: ***
  NOTES_MYSQL_USERNAME: prod-notes
```

will be transposed to the following:

```bash
  echo "TUTOR_MYSQL_HOST=your-rds-instance.xyzw93ffci2g.us-east-2.rds.amazonaws.com" >> $GITHUB_ENV
  echo "TUTOR_MYSQL_PORT=3306" >> $GITHUB_ENV
  echo "TUTOR_NOTES_MYSQL_PASSWORD=***" >> $GITHUB_ENV
  echo "TUTOR_NOTES_MYSQL_USERNAME=prod-notes"  >> $GITHUB_ENV
```

This action is designed to work seamlessly with Kubernetes secrets created by the Terraform modules contained in [Cookiecutter Tutor Open edX Production Devops Tools](https://github.com/lpm0073/cookiecutter-openedx-devops).

**IMPORTANT SECURITY DISCLAIMER**: Sensitive data contained in the Kubernetes secrets is masked in Github Actions logs and console output, while other non-sensitive data is intentionally exposed for diagnostics and trouble shooting purposes. The example above is illustrative of this behavior. Be aware however that this behavior is specific to the secrets that are created in the Cookiecutter and thus, this behavior will **NOT** automatically transcend itself for other non-Cookiecutter Kubernetes secrets you might have created.


## Usage:


```yaml
name: Example workflow

on: workflow_dispatch

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # required antecedent
      - uses: actions/checkout@v3.5.0

      # required antecedent
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.THE_NAME_OF_YOUR_AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.THE_NAME_OF_YOUR_AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-2

      # Intialize the ubuntu environment
      - name: Configure Github workflow environment
        uses: openedx-actions/tutor-k8s-init@v1.0.8
        with:
          eks-namespace: ${{ env.NAMESPACE }}
          eks-cluster-name: ${{ env.EKS_CLUSTER_NAME }}
          aws-region: ${{ env.AWS_REGION }}

      # This action
      - name: Get a secret
        uses: openedx-actions/tutor-k8s-get-secret@v1.0.0
        with:
          eks-namespace: openedx-prod
          eks-secret-name: mysql-root
```
