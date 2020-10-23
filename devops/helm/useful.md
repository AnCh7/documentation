#### Install old version

```shell
# unlink the current version if it exist
brew unlink kubernetes-helm

# 2.14.3
cd ~/Downloads
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

## 