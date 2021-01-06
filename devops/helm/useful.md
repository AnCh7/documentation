### Install old version

```shell
# unlink the current version if it exist
brew unlink kubernetes-helm

# 2.14.3
curl -O https://raw.githubusercontent.com/Homebrew/homebrew-core/0a17b8e50963de12e8ab3de22e53fccddbe8a226/Formula/kubernetes-helm.rb
brew install kubernetes-helm.rb
rm kubernetes-helm.rb

# switch between installed versions
brew switch kubernetes-helm 2.14.3

brew pin kubernetes-helm

where helm
# /usr/local/opt/helm@2/bin/helm
# /usr/local/bin/helm
```

### Upgrading Tiller:

```bash
# Install 2.17.0 client
curl -O https://raw.githubusercontent.com/Homebrew/homebrew-core/49ee38ed0e5c387f3c9c792ddec7b84952ede3e5/Formula/helm@2.rb
brew install helm@2.rb

brew unlink kubernetes-helm && brew switch helm@2 2.17.0

helm repo add stable https://charts.helm.sh/stable

# Upgrade Tiller
kubectl --namespace=kube-system set image deployments/tiller-deploy tiller=gcr.io/kubernetes-helm/tiller:v2.17.0

# Rollback
kubectl --namespace=kube-system set image deployments/tiller-deploy tiller=gcr.io/kubernetes-helm/tiller:v2.14.3

# Verify
helm version
```
