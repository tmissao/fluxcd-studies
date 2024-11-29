# Kustomize K8s Manifests

This release intends to explore how FluxCD works together with Kustomize, if you are not familiar with Kustomize check this [project](https://github.com/tmissao/k8s-kustomization-studies). The idea here is to have two versions of the same webserver.

- [nginx-dev](./artifacts/fluxcd/apps/nginx-dev/) - Nginx application which should use less resource than production and run on dev namespace, also use higher nginx image version (1.27)

- [nginx-prod](./artifacts/fluxcd/apps/nginx-prod/) - Nginx application which uses more computational resources and run on prod namespace using a more stable nginx image version (1.21)

Both application have a [common base](./artifacts/kustomize/base/) standard K8s manifest. So each application use Kustomize to overwrite the required configuration [overlays/dev](./artifacts/kustomize/overlays/dev/) for dev environment and [overlays/prod](./artifacts/kustomize/overlays/prod/) for prod environment. 

## Setup

These are the steps required in order to following this guide

1. Create the application namespace
```bash
kubectl create namespace dev
kubectl create namespace prod
```

2. Apply FluxCD manifests
```bash
# Development Application
kubectl apply -f ./artifacts/fluxcd/apps/nginx-dev/gitRepository.yaml 
kubectl apply -f ./artifacts/fluxcd/apps/nginx-dev/kustomization.yml

# Production Application
kubectl apply -f ./artifacts/fluxcd/apps/nginx-prod/gitRepository.yaml 
kubectl apply -f ./artifacts/fluxcd/apps/nginx-prod/kustomization.yml
```

## Results

```bash
kubectl get deploy -n dev -o wide
# NAME        READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES       SELECTOR
# nginx-dev   2/2     2            2           8m32s   nginx        nginx:1.27   app=nginx,env=dev

kubectl get deploy -n prod -o wide
# NAME         READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES       SELECTOR
# nginx-prod   3/3     3            3           7m34s   nginx        nginx:1.21   app=nginx,env=prod
```