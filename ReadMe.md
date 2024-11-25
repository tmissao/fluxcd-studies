# Flux Studies

- Install Kubectl - https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
- Install Kind - https://kind.sigs.k8s.io/docs/user/quick-start
- Install Flux - https://fluxcd.io/flux/installation/
- Create a Git PAT - 
- Create Kubectl Cluster
```bash
kind create cluster --name flux-demo --config ./artifacts/code/kind/multi-node.yml --wait 5m
kubectl config use-context kind-flux-demo
kubectl cluster-info
```

- Deploy Flux
```
export GITHUB_TOKEN=<gh-token>
flux bootstrap github \
  --token-auth \
  --owner=tmissao \
  --repository=fluxcd-studies \
  --branch=master    \
  --path=artifacts/clusters/flux-demo \
  --personal
```