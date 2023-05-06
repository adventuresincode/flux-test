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
git pull 
```
```sh
#Create a flux source 
mkdir ./clusters/$CLUSTER_NAME
mkdir ./clusters/$CLUSTER_NAME/gatekeeper/
export OPA_BOOTSTRAPPER_VERSION="release-3.12"
flux create source git gatekeeper \
  --namespace=gatekeeper-system \
  --url=https://github.com/open-policy-agent/gatekeeper \
  --branch=$OPA_BOOTSTRAPPER_VERSION \
  --interval=30s \
  --export > ./clusters/$CLUSTER_NAME/gatekeeper/gatekeeper-source.yaml

git add -A && git commit -m "Added opa-gatekeeper GitRepository"
git push

flux create kustomization gatekeeper \
--namespace=gatekeeper-system \
--source=gatekeeper \
--path="./deploy" \
--prune=true \
--interval=5m \
--export > ./clusters/$CLUSTER_NAME/gatekeeper/gatekeeper-kustomization.yaml

git add -A && git commit -m "Added gatekeeper Kustomization"
git push
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