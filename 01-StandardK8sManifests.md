# FluxCD with Standard K8s Manifests

This release intends to explore how FluxCD works, investigating FluxCD workflow and core components such as: [GitRepository](https://fluxcd.io/flux/components/source/gitrepositories/) and [Kustomize](https://fluxcd.io/flux/components/kustomize/kustomizations/#writing-a-kustomization-spec).

So, here we have a basic python Flask application, and every time the K8s manifest are update in the GitRepository we would like to update our deployment

## Setup

These are the steps required in order to following this guide

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

4. Create Apps namespace
```bash
kubectl create namespace apps
```

5. Wait Flux to Reconciliate the deployments

6. Check your deployments
```bash
kubectl get deployments -n apps
```

7. Port Forward the Application
```bash
kubectl port-forward service/example-app-1 5000:80 -n apps
```

8. Check the result
```bash
curl localhost:5000
# {"message":"Welcome to the Flask app!" Version 1.0.0}
```

9. Update the [app file](./artifacts/python/example-app/src/app.py) increasing the version to 1.0.1
```python
@app.route('/')
def home():
    return jsonify({"message": "Welcome to the Flask app! Version 1.0.1"})
```

10. Rebuild the docker image increasing the version label and load it on Kind
```bash
cd artifacts/python/example-app/src
docker build -t example-app-1.0.1 .
kind load docker-image example-app-1.0.1 --name demo
```

11. Update the k8s [deployment manifest](./artifacts/python/example-app/k8s/deployment.yaml) to use the new image
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

12. Commit your change and watch flux to deploy the new version, the update could be checked analizing the kustomization resource
```bash
kubectl get kustomization -n apps
```

13. Create a new port-forward and check the result
```bash
kubectl port-forward service/example-app-1 5000:80 -n apps
curl localhost:5000
{"message":"Welcome to the Flask app! Version 1.0.1"}
```