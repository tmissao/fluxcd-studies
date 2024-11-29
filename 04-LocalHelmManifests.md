# Local Helm Manifests

This release intends to explore how FluxCD works with Helm Releases being the helm charts located on git repository:

So, on this demo it is desired to install the local [example-app helm chart](./artifacts/python/example-app/helm/)

1. Create the application namespace
```bash
kubectl create namespace apps
```

2. Build Python App Image
```bash
cd artifacts/python/example-app/src
docker build -t example-app-1.0.0 .
```

3. Load Python App image on K8s via Kind
```bash
kind load docker-image example-app-1.0.0 --name demo
```

4. Apply the FluxCD Manifests
```bash
kubectl apply -f ./artifacts/fluxcd/apps/example-app-helm
```

6. Check Helm Releases
```bash
kubectl get helmreleases -n apps
# NAME          AGE   READY   STATUS
# webapp-helm   91m   True    Helm upgrade succeeded for release apps/webapp-helm.v3 with chart webapp-helm@0.1.1

kubect get pods -n apps
# NAME                           READY   STATUS    RESTARTS   AGE
# webapp-helm-5888bdd468-r2f25   1/1     Running   0          78m
```

7. Port Forward the Application
```bash
kubectl port-forward service/webapp-helm 5000:5000 -n apps
```

8. Check the result
```bash
curl localhost:5000
# {"message":"Welcome to the Flask app!" Version 1.0.0}
```