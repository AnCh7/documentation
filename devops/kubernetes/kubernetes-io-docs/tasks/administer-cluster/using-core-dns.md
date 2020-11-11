You can use CoreDNS instead of kube-dns in your cluster by replacing kube-dns in an existing deployment, or by using tools like kubeadm that will deploy and upgrade the cluster for you.

Install CoreDNS: see the documentation at the [CoreDNS GitHub project.](https://github.com/coredns/deployment/tree/master/kubernetes)

You can also move to CoreDNS when you use `kubeadm` to upgrade a cluster that is using `kube-dns`. In this case, `kubeadm` will generate the CoreDNS configuration (“Corefile”) based upon the `kube-dns` ConfigMap, preserving configurations for federation, stub domains, and upstream name server.

