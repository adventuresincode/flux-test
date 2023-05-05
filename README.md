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
export OWNER="adventuresincode"
export REPO="flux-test"

flux bootstrap github \
  --owner=$OWNER \
  --repository=$REPO \
  --branch=main \
  --path=./clusters/$CLUSTER_NAME \
  --personal

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