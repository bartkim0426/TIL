# argo workflow 101 in eks


## Install argo CLI


install argo in mac

```
# Download the binary
curl -sLO https://github.com/argoproj/argo/releases/download/v3.1.0-rc14/argo-darwin-amd64.gz

# Unzip
gunzip argo-darwin-amd64.gz

# Make binary executable
chmod +x argo-darwin-amd64

# Move binary to path
mv ./argo-darwin-amd64 /usr/local/bin/argo

# Test installation
argo version
```

## Deploy argo in k8s

```
export ARGO_VERSION="v3.1.0"
# or use stable
export ARGO_VERSION="stable"

kubectl create namespace argo
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/${ARGO_VERSION}/manifests/install.yaml
```

(Optional for only EKS) configure serviceaccount to run workflows

```
kubectl -n argo create rolebinding default-admin --clusterrole=admin --serviceaccount=argo:default
```

### Configure artifact repository

argo use artifact repository to pass data between jobs in a workflow.

Can use S3, github, and so on.

This example, we'll use S3 for artifact repository.


#### Create S3 bucket

```
aws s3 mb s3://<bucket_name>
```

#### Patch artifactRepository in argo workflow-controller-configmap

create patch file

```
cat <<EoF > argo-patch.yaml
data:
  config: |
    artifactRepository:
      s3:
        bucket: batch-artifact-repository-${ACCOUNT_ID}
        endpoint: s3.amazonaws.com
EoF
```

deploy the patch

```
kubectl -n argo patch \
  configmap/workflow-controller-configmap \
  --patch "$(argo-patch.yaml)"
```

verify configmap

```
kubectl -n argo get configmap/workflow-controller-configmap -o yaml
```

#### Create an IAM policy

To read/wirte in S3 bucket, need to add policy and add it to EC2 instance profile of the worker nodes.


Get ROLE_NAME from eks stack

```
STACK_NAME=$(eksctl get nodegroup --cluster eksworkshop-eksctl -o json | jq -r '.[].StackName')
ROLE_NAME=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType=="AWS::IAM::Role") | .PhysicalResourceId')
echo "export ROLE_NAME=${ROLE_NAME}" | tee -a ~/.bash_profile
```

verify `$ROLE_NAME` env is set

```
test -n "$ROLE_NAME" && echo ROLE_NAME is "$ROLE_NAME" || echo ROLE_NAME is not set
```

Create policy file

```
cat <<EoF > k8s-s3-policy.json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:*"
      ],
      "Resource": [
        "arn:aws:s3:::batch-artifact-repository-${ACCOUNT_ID}",
        "arn:aws:s3:::batch-artifact-repository-${ACCOUNT_ID}/*"
      ]
    }
  ]
}
EoF
```

Attach it via aws cli

```
aws iam put-role-policy --role-name $ROLE_NAME --policy-name S3-Policy-For-Worker --policy-document file://k8s-s3-policy.json
```

verify policy

```
aws iam get-role-policy --role-name $ROLE_NAME --policy-name S3-Policy-For-Worker
```

### Simple batch workflow

```
cat <<EoF > workflow-whalesay.yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: whalesay-
spec:
  entrypoint: whalesay
  templates:
  - name: whalesay
    container:
      image: docker/whalesay
      command: [cowsay]
      args: ["This is an Argo Workflow!"]
EoF
```

run workflow with argo cli

```
argo submit --watch workflow-whalesay.yaml --namespace=aro
```


