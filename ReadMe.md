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

6. Wait Flux to Reconciliate the deployments

7. Check your deployments
```bash
kubectl get deployments -n apps
```

8. Port Forward the Application
```bash
kubectl port-forward service/example-app-1 5000:80 -n apps
```

9. Check the result
```bash
curl localhost:5000
# {"message":"Welcome to the Flask app!" Version 1.0.0}
```

10. Update the [app file](./artifacts/python/example-app/src/app.py) increasing the version to 1.0.1
```python
@app.route('/')
def home():
    return jsonify({"message": "Welcome to the Flask app! Version 1.0.1"})
```

11. Rebuild the docker image increasing the version label and load it on Kind
```bash
cd artifacts/python/example-app/src
docker build -t example-app-1.0.1 .
kind load docker-image example-app-1.0.1 --name demo
```

12. Update the k8s [deployment manifest](./artifacts/python/example-app/k8s/deployment.yaml) to use the new image
```yaml
...
spec:
    containers:
    - name: example-app-1
      image: example-app-1.0.1
      imagePullPolicy: Never
      ports:
      - containerPort: 5000
```

13. Commit your change and watch flux to deploy the new version, the update could be checked analizing the kustomization resource
```bash
kubectl get kustomization -n apps
```

14. Create a new port-forward and check the result
```bash
kubectl port-forward service/example-app-1 5000:80 -n apps
curl localhost:5000
{"message":"Welcome to the Flask app! Version 1.0.1"}
```

## Resources
---

- [Documentation](https://fluxcd.io/flux/concepts/)
- [Tutorial](https://www.youtube.com/watch?v=X5W_706-jSY)