<img src="https://avatars.githubusercontent.com/u/40179672" width="75">

[![hack.d Lawrence McDaniel](https://img.shields.io/badge/hack.d-Lawrence%20McDaniel-orange.svg)](https://lawrencemcdaniel.com)
[![discuss.overhang.io](https://img.shields.io/static/v1?logo=discourse&label=Forums&style=flat-square&color=ff0080&message=discuss.overhang.io)](https://discuss.overhang.io)
[![docs.tutor.overhang.io](https://img.shields.io/static/v1?logo=readthedocs&label=Documentation&style=flat-square&color=blue&message=docs.tutor.overhang.io)](https://docs.tutor.overhang.io)<br/>
[![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)
[![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)

# tutor-k8s-get-secret

Github Action to extract and decrypt secrets data from k8s and transpose into environment variables that can be consumed by tutor with a ´tutor config save´ operation. a k8s secret of the form:

```bash
  MYSQL_HOST: stepwisemath-global-live.xyzw93ffci2g.us-east-2.rds.amazonaws.com
  MYSQL_PORT: "3306"
  NOTES_MYSQL_PASSWORD: your-strong-password
  NOTES_MYSQL_USERNAME: prod-notes
```

will be transposed to the following:

```bash
  echo "TUTOR_MYSQL_HOST=stepwisemath-global-live.xyzw93ffci2g.us-east-2.rds.amazonaws.com" >> $GITHUB_ENV
  echo "TUTOR_MYSQL_PORT=3306" >> $GITHUB_ENV
  echo "TUTOR_NOTES_MYSQL_PASSWORD=BYMNZhquxgqUCbqv" >> $GITHUB_ENV
  echo "TUTOR_NOTES_MYSQL_USERNAME=prod-notes"  >> $GITHUB_ENV
```


## Usage:


```yaml
name: Example workflow

on: workflow_dispatch

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # required antecedent
      - uses: actions/checkout@v3.0.2

      # required antecedent
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1.6.1
        with:
          aws-access-key-id: ${{ secrets.THE_NAME_OF_YOUR_AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.THE_NAME_OF_YOUR_AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-2

      # Intialize the ubuntu environment
      - name: Configure Github workflow environment
        uses: openedx-actions/tutor-k8s-init@v0.0.1
        with:
          eks-namespace: ${{ env.NAMESPACE }}
          eks-cluster-name: ${{ env.EKS_CLUSTER_NAME }}
          aws-region: ${{ env.AWS_REGION }}

      # This action
      - name: Get a secret
        uses: openedx-actions/tutor-k8s-get-secret@v0.0.2   
        with:
          eks-namespace: openedx-prod
          eks-secret-name: mysql-root
```
