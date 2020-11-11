Create a simple Pod to use as a test environment

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dnsutils
  namespace: default
spec:
  containers:
  - name: dnsutils
    image: gcr.io/kubernetes-e2e-test-images/dnsutils:1.3
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
  restartPolicy: Always
```

```shell
kubectl apply -f dnsutils.yaml
kubectl exec -ti dnsutils -- nslookup kubernetes.default
```

Check the local DNS configuration first

```shell
kubectl exec dnsutils cat /etc/resolv.conf
```

Verify that the search path and name server are set up like the following (note that search path may vary for different cloud providers).

Check if the DNS pod is running

```shell
kubectl get pods --namespace=kube-system -l k8s-app=kube-dns
```

Check for Errors in the DNS pod

```shell
kubectl logs --namespace=kube-system $(kubectl get pods --namespace=kube-system -l k8s-app=kube-dns -o name | head -1) -c kubedns

kubectl logs --namespace=kube-system $(kubectl get pods --namespace=kube-system -l k8s-app=kube-dns -o name | head -1) -c dnsmasq

kubectl logs --namespace=kube-system $(kubectl get pods --namespace=kube-system -l k8s-app=kube-dns -o name | head -1) -c sidecar
```

Is DNS service up?

```shell
kubectl get svc --namespace=kube-system
```

Are DNS endpoints exposed?

```shell
kubectl get ep kube-dns --namespace=kube-system
```

Are DNS queries being received/processed?