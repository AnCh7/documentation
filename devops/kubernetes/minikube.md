## Minikube

[Minikube](https://minikube.sigs.k8s.io/)

Minikube implements a local Kubernetes cluster on macOS, Linux, and Windows. It runs the latest stable release of Kubernetes, with support for standard Kubernetes features and it is good for developing local Kubernetes applications.

#### Install

```bash
brew cask install minikube

# Uninstall
brew uninstall minikube
rm -rf ~/.minikube
```

#### Start

```bash
# Start a cluster
minikube start

# Access the Kubernetes Dashboard running within the minikube cluster
minikube dashboard --url
```

#### Interact

Once started, you can interact with your cluster using `kubectl`, just like any other Kubernetes cluster. 

#### Stop

```bash
# Stop your local cluster
minikube stop

# Delete your local cluster
minikube delete
```

#### Using addons

minikube has a set of built-in addons that, when enabled, can be used within Kubernetes.

```bash
minikube addons list
```

#### Filesystem mounts

```bash
# To mount a directory from the host into the guest using the mount subcommand
minikube mount <source directory>:<target directory>
```

#### LoadBalancer access

```bash
# Services of type LoadBalancer can be exposed via the minikube tunnel
minikube tunnel

# Cleaning up orphaned routes
minikube tunnel --cleanup
```

#### NodePort access

```bash
# Getting the NodePort using the service command
minikube service --url $SERVICE

# Getting the NodePort using kubectl
kubectl get service $SERVICE --output='jsonpath="{.spec.ports[0].nodePort}"'
```

#### Debugging

```bash
# Enabling debug logs
# --v=0 INFO logs
# --v=1 WARNING logs
# --v=2 ERROR logs
# --v=3 libmachine logging
# --v=7 libmachine –debug level logging
minikube start --v=7

# VM logs
minikube logs

# Viewing pod status
kubectl get po -A

# Detailed information about a pod
kubectl describe pod <name> -n <namespace>
```

#### Use minikube's built-in docker daemon

```bash
eval $(minikube docker-env)
```

Add this line to `.bash_profile` or `.zshrc` if you want to use minikube's daemon by default.
You can revert back to the host docker daemon by running:

```bash
eval $(docker-machine env -u)
```

#### Setup a local registry, so Kubernetes can pull the image(s) from there

```bash
# https://hub.docker.com/_/registry
docker run -d -p 5000:5000 --restart=always --name registry registry:2

# Build image
docker build --rm -t app_name:latest .
docker tag app_name localhost:5000/app_name:latest
docker push localhost:5000/app_name:latest

# Update your Kubernetes configuration to use local docker registry
image: localhost:5000/app_name:latest

# Use minikube's built-in docker daemon
eval $(minikube docker-env)

# Check new image
docker images
```
