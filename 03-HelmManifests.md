# Helm Manifests

This release intends to explore how FluxCD works with Helm Releases and for that the following core components were explored:

- [Helm Repository](https://fluxcd.io/flux/components/source/helmrepositories/) - defines a source of Helm charts. This can be a remote Helm chart repository (e.g., from a public or private Helm registry) or a local OCI Helm repository.
- [Helm Release](https://fluxcd.io/flux/components/helm/helmreleases/) - The HelmRelease resource defines a release of a specific Helm chart

So, on this demo it is desired to install the community chart [kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack) responsible to provide observability to our cluster.

## Setup
---

These are the steps required in order to following this guide

1. Create the application namespace
```bash
kubectl create namespace monitoring
```

2. Apply FluxCD manifests
```bash
kubectl apply -f ./artifacts/fluxcd/infrastructure/prometheus/helmRepository.yaml
kubectl apply -f ./artifacts/fluxcd/infrastructure/prometheus/helmRelease.yaml 
```

## Results

```bash
kubectl get helmrelease -n monitoring
# NAME                    AGE   READY   STATUS
# kube-prometheus-stack   64m   True    Helm install succeeded for release monitoring/kube-prometheus-stack.v1 with chart kube-prometheus-stack@66.3.0

kubectl get pods -n monitoring
# NAME                                                        READY   STATUS    RESTARTS   AGE
# alertmanager-kube-prometheus-stack-alertmanager-0           2/2     Running   0          65m
# kube-prometheus-stack-grafana-5944686f89-ldkhb              3/3     Running   0          65m
# kube-prometheus-stack-kube-state-metrics-847c7bf499-kvxd5   1/1     Running   0          65m
# kube-prometheus-stack-operator-77665c97db-79tjw             1/1     Running   0          65m
# kube-prometheus-stack-prometheus-node-exporter-bbw2p        1/1     Running   0          65m
# kube-prometheus-stack-prometheus-node-exporter-qff49        1/1     Running   0          65m
# kube-prometheus-stack-prometheus-node-exporter-sd9bj        1/1     Running   0          65m
# prometheus-kube-prometheus-stack-prometheus-0               2/2     Running   0          65m

```
