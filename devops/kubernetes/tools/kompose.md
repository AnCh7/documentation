## Kompose

**Kubernetes + Compose = Kompose**

Kompose has support for two providers: OpenShift and Kubernetes. You can choose a targeted provider using global option `--provider`. If no provider is specified, Kubernetes is set by default.

Kompose supports conversion of V1, V2, and V3 Docker Compose files.

### Install

```bash
brew install kompose
```

### Minikube

```bash
# Start minikube
minikube start

# Download an example Docker Compose file, or use your own:
wget https://raw.githubusercontent.com/kubernetes/kompose/master/examples/docker-compose.yaml

# Convert your Docker Compose file to Kubernetes and deploy
kompose convert
kubectl create -f

# Alternatively, you can convert and deploy directly to Kubernetes
kompose up

# Access the newly deployed service:
minikube service XXXXXX

# Use kubectl to see what IP the service is using
kubectl get deployment,svc,pods
kubectl describe svc XXXXXX

# Take the application out
kompose down
```

### Minishift

```bash
# Start minishift
minishift start

# Download an example Docker Compose file, or use your own:
wget https://raw.githubusercontent.com/kubernetes/kompose/master/examples/docker-compose.yaml

# Convert your Docker Compose file to OpenShift
kompose convert --provider=openshift

# Alternatively, you can convert and deploy directly to OpenShift
kompose up --provider=openshift

# Create a route for the service 
oc expose service/XXXXXX

# Access the service with minishift
minishift openshift service XXXXXX --namespace=myproject

# GUI interface of OpenShift
minishift console
```

### Build and Push Docker Images

Kompose supports both building and pushing Docker images. When using the `build` key within your Docker Compose file, your image will:

- Automatically be built with Docker using the `image` key specified within your file
- Be pushed to the correct Docker repository using local credentials (located at `.docker/config`)

###  Alternative Conversions

The default `kompose` transformation will generate Kubernetes [Deployments](http://kubernetes.io/docs/user-guide/deployments/) and [Services](http://kubernetes.io/docs/user-guide/services/), in yaml format. You have alternative option to generate json with `-j`. Also, you can alternatively generate [Replication Controllers](http://kubernetes.io/docs/user-guide/replication-controller/) objects, [Daemon Sets](http://kubernetes.io/docs/admin/daemons/), or [Helm](https://github.com/helm/helm) charts.
