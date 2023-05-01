# flux-test

Create a local kind cluster
```sh
export CLUSTER_NAME=c002
kind create cluster --name $CLUSTER_NAME
```
Install flux locally

Bootstrap the cluster
```sh
export GITHUB_USER=your_username
export GITHUB_TOKEN=your_personal_access_token

export BRANCH_NAME="test-2"

flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=flux-config \
  --branch=$BRANCH_NAME \
  --path=./clusters/$CLUSTER_NAME \
  --personal

```
