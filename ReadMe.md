# Flux Studies

## Requisites
---

1. [Install Docker](https://docs.docker.com/engine/install/ubuntu/)
2. [Install Kind](https://kind.sigs.k8s.io/docs/user/quick-start)
3. [Install Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)
4. [Install Flux](https://fluxcd.io/flux/installation/)
5. Create a Github Repo - Will be used by Flux
6. [Create a Github PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)

## Setup
---

These are the steps required in order to following this guide


1. Create a local Kubernetes Cluster via Kind
```bash
kind create cluster --name demo --config ./artifacts/kind/multi-node.yml
kubectl config use-context kind-demo
```

2. Deploy Flux
```bash
export GITHUB_TOKEN=<gh-token> # Add the your PAT Token here
flux bootstrap github \
  --token-auth \
  --owner=tmissao \
  --repository=fluxcd-studies \
  --branch=master    \
  --path=artifacts/fluxcd/clusters/demo \
  --personal \
  --namespace=flux-system
```

3. Build Python App Image
```bash
cd artifacts/python/example-app/src
docker build -t example-app-1.0.0 .
```

4. Load Python App image on K8s via Kind
```bash
kind load docker-image example-app-1.0.0 --name demo
```

5. Create Apps namespace
```bash
kubectl create namespace apps
```