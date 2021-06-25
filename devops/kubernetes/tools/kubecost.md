### Kubecost

> References:
>
> https://github.com/kubecost
>
> https://www.kubecost.com



## Installation

Prerequisites:

```shell
minikube start
 
asdf install helm 2.17.0
asdf global helm 2.17.0
 
kubectl config use-context minikube && k9s --context minikube

helm init
kubectl get pods --namespace kube-system # check tiller pod
helm version
```

Install:

```shell
# Install Kubecost
helm repo add kubecost https://kubecost.github.io/cost-analyzer/
helm install kubecost/cost-analyzer --namespace kubecost --name kubecost --set kubecostToken="YXNkZkB0dXQuYnk=xm343yadf98"

# minikube specific
kubectl edit cm nginx-conf -n kubecost
# Change:
# kubecost-cost-analyzer.kubecost:9001 ---> localhost:9001
# kubecost-cost-analyzer.kubecost:9003 ---> localhost:9003
# Restart the kubecost-cost-analyzer pod in the kubecost namespace

# Enable port-forward
kubectl port-forward --namespace kubecost deployment/kubecost-cost-analyzer 9090
```

Check data:

http://localhost:9090

