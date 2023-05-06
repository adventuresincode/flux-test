# flux-test

Create a local kind cluster
```sh
export CLUSTER_NAME=c005
kind create cluster --name $CLUSTER_NAME
```
Install flux locally

Bootstrap the cluster
```sh
export GITHUB_USER=your_username
export GITHUB_TOKEN=your_personal_access_token

export BRANCH_NAME="test-003"
export OWNER="adventuresincode"
export REPO="flux-test"

flux bootstrap github \
  --owner=$OWNER \
  --repository=$REPO \
  --branch=$BRANCH_NAME \
  --path=./clusters/$CLUSTER_NAME \
  --personal

```
```sh
#Create a flux source 
mkdir ./clusters/$CLUSTER_NAME
mkdir ./clusters/$CLUSTER_NAME/gatekeeper/
flux create kustomization gatekeeper \
--namespace=gatekeeper-system \
--service-account=opa-gatekeeper \
--source=gatekeeper \
--path="./" \
--prune=true \
--interval=5m \
--export > ./clusters/$CLUSTER_NAME/gatekeeper/gatekeeper-kustomization.yaml
```

Add a repository to flux
```sh
export URL="https://github.com/stefanprodan/podinfo"
export YAML_FILE="podinfo-source.yaml"
flux create source git podinfo \
  --url=$URL \
  --branch=master \
  --interval=30s \
  --export > ./clusters/$CLUSTER_NAME/$YAML_FILE
  ```


  kubectl create clusterrolebinding cluster-admin-binding \
    --clusterrole cluster-admin \
    --user opa-gatekeeper

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: gatekeeper-system
  name: opa-gatekeeper
rules:
- apiGroups: ["apiextensions.k8s.io"] 
  resources: ["customresourcedefinitions"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```  