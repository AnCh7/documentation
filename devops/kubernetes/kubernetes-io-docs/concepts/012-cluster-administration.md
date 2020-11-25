## Certificates

When using client certificate authentication, you can generate certificates manually through `easyrsa`, `openssl` or `cfssl`.

A client node may refuse to recognize a self-signed CA certificate as valid. For a non-production deployment, or for a deployment that runs behind a company firewall, you can distribute a self-signed CA certificate to all clients and refresh the local list for valid certificates.

You can use the `certificates.k8s.io` API to provision x509 certificates to use for authentication as documented [here](https://kubernetes.io/docs/tasks/tls/managing-tls-in-a-cluster).

## Cloud Providers

How to manage Kubernetes running on a specific cloud provider:
- [AWS](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#aws)
- [Azure](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#azure)
- [CloudStack](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#cloudstack)
- [GCE](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#gce)
- [OpenStack](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#openstack)
- [OVirt](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#ovirt)
- [Photon](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#photon)
- [vSphere](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#vsphere)
- [IBM Cloud Kubernetes Service](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#ibm-cloud-kubernetes-service)
- [Baidu Cloud Container Engine](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#baidu-cloud-container-engine)
- [Tencent Kubernetes Engine](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#tencent-kubernetes-engine)

[kubeadm](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm/) is a popular option for creating kubernetes clusters. kubeadm has configuration options to specify configuration information for cloud providers. The in-tree cloud providers typically need both `--cloud-provider` and `--cloud-config` specified in the command lines for the [kube-apiserver](https://kubernetes.io/docs/admin/kube-apiserver/), [kube-controller-manager](https://kubernetes.io/docs/admin/kube-controller-manager/) and the [kubelet](https://kubernetes.io/docs/admin/kubelet/). The contents of the file specified in `--cloud-config` for each provider is documented below as well.


## Managing Resources

Kubernetes provides a number of tools to help you manage your application deployment, including scaling and updating.

**Organizing resource configurations**

Many applications require multiple resources to be created, such as a  Deployment and a Service. Management of multiple resources can be  simplified by grouping them together in the same file (separated by `---` in YAML).

The resources will be created in the order they appear in the file.  Therefore, it’s best to specify the service first, since that will  ensure the scheduler can spread the pods associated with the service as  they are created by the controller(s), such as Deployment.

**Bulk operations in kubectl**

Resource creation isn’t the only operation that `kubectl` can perform in bulk. It can also extract resource names from configuration files in order to perform other operations.

In the case of just two resources, it’s also easy to specify both on the command line using the resource/name syntax:
```shell
kubectl delete deployments/my-nginx services/my-nginx-svc
```

For larger numbers of resources, you’ll find it easier to specify the selector:
```powershell
kubectl delete deployment,services -l app=nginx
```

You can recursively perform the operations on the subdirectories also, by specifying `--recursive` or `-R` alongside the `--filename,-f` flag.

**Using labels effectively**

The labels allow us to slice and dice our resources along any dimension specified by a label:

```shell
kubectl get pods -Lapp -Ltier -Lrole

NAME                           READY     STATUS    RESTARTS   AGE       APP         TIER       ROLE
guestbook-fe-4nlpb             1/1       Running   0          1m        guestbook   frontend   <none>
guestbook-fe-ght6d             1/1       Running   0          1m        guestbook   frontend   <none>
guestbook-fe-jpy62             1/1       Running   0          1m        guestbook   frontend   <none>
guestbook-redis-master-5pg3b   1/1       Running   0          1m        guestbook   backend    master
guestbook-redis-slave-2q2yf    1/1       Running   0          1m        guestbook   backend    slave
guestbook-redis-slave-qgazl    1/1       Running   0          1m        guestbook   backend    slave
my-nginx-divi2                 1/1       Running   0          29m       nginx       <none>     <none>
my-nginx-o0ef1                 1/1       Running   0          29m       nginx       <none>     <none>
```

**Canary deployments**

It is common practice to deploy a canary of a new  application release (specified via image tag in the pod template) side  by side with the previous release so that the new release can receive  live production traffic before fully rolling it out.

For instance, you can use a `track` label to differentiate different releases. The primary, stable release would have a `track` label with value as `stable`.

**Updating labels**

Sometimes existing pods and other resources need to be relabeled before creating new resources. This can be done with `kubectl label`.

**Updating annotations**

Sometimes you would want to attach annotations to resources. Annotations are arbitrary non-identifying metadata for retrieval by API clients  such as tools, libraries, etc. This can be done with `kubectl annotate`. 

**Scaling your application**

When load on your application grows or shrinks, it’s easy to scale with `kubectl scale`.  For more information, please see [kubectl scale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/#scale), [kubectl autoscale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/#autoscale) and [horizontal pod autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) documents.

**In-place updates of resources**

1. kubectl apply - This command will compare the version of the configuration that you’re  pushing with the previous version and apply the changes you’ve made,  without overwriting any automated changes to properties you haven’t  specified. Note that `kubectl apply` attaches an annotation to the  resource in order to determine the changes to the configuration since  the previous invocation. When it’s invoked, `kubectl apply`  does a three-way diff between the previous configuration, the provided  input and the current configuration of the resource, in order to  determine how to modify the resource.
2. kubectl edit - This is equivalent to first `get` the resource, edit it in text editor, and then `apply` the resource with the updated version.
3. kubectl patch - This command supports JSON patch, JSON merge patch, and strategic merge patch. See [Update API Objects in Place Using kubectl patch](https://kubernetes.io/docs/tasks/run-application/update-api-object-kubectl-patch/) and [kubectl patch](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/#patch).

**Disruptive updates**

Use `replace --force`, which deletes and re-creates the resource.


## Cluster Networking

There are 4 distinct networking problems to address:
1. Highly-coupled container-to-container communications: this is solved by [pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/) and `localhost` communications.
2. Pod-to-Pod communications: this is the primary focus of this document.
3. Pod-to-Service communications: this is covered by [services](https://kubernetes.io/docs/concepts/services-networking/service/).
4. External-to-Service communications: this is covered by [services](https://kubernetes.io/docs/concepts/services-networking/service/).

Kubernetes is all about sharing machines between applications. Typically, sharing machines requires ensuring that two applications do not try to use the same ports. Dynamic port allocation brings a lot of complications to the system - every application has to take ports as flags, the API servers have to know how to insert dynamic port numbers into configuration blocks, services have to know how to find each other, etc.

#### The Kubernetes network model

Every `Pod` gets its own IP address. This means you do not need to explicitly create links between `Pods` and you almost never need to deal with mapping container ports to host ports. This creates a clean, backwards-compatible model where `Pods` can be treated much like VMs or physical hosts from the perspectives of port allocation, naming, service discovery, load balancing, application configuration, and migration:
- pods on a node can communicate with all pods on all nodes without NAT
- agents on a node (e.g. system daemons, kubelet) can communicate with all pods on that node
- pods in the host network of a node can communicate with all pods on all nodes without NAT

Kubernetes IP addresses exist at the `Pod` scope - containers within a `Pod` share their network namespaces - including their IP address. Containers within a `Pod` can all reach each other’s ports on `localhost`. Containers within a `Pod` must coordinate port usage, but this is no different from processes in a VM.

There are a number of ways that this network model can be implemented: AWS VPC CNI for Kubernetes, Flannel, Google Compute Engine (GCE) etc.


## Logging Architecture

Logs should have a separate storage and lifecycle independent of nodes, pods, or containers. This concept is called cluster-level-logging. Kubernetes provides no native storage solution for log data, but you can integrate many existing logging solutions into your Kubernetes cluster.

**Basic logging in Kubernetes**

You can use `kubectl logs` to retrieve logs from a previous instantiation of a container with `--previous` flag, in case the container has crashed.

**Logging at the node level**

![Node level logging](.012-cluster-administration-images/logging-node-level.png)

Everything a containerized application writes to `stdout` and `stderr` is handled and redirected somewhere by a container engine. For example, the Docker container engine redirects those two streams to [a logging driver](https://docs.docker.com/engine/admin/logging/overview), which is configured in Kubernetes to write to a file in json format.

If a container restarts, the kubelet keeps one terminated container with its logs.

An important consideration in node-level logging is implementing log rotation, so that logs don’t consume all available storage on the node. ubernetes currently is not responsible for rotating logs.

When you run [`kubectl logs`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) as in the basic logging example, the kubelet on the node handles the request and reads directly from the log file, returning the contents in the response.

There are two types of system components: those that run in a container (Kubernetes scheduler and kube-proxy) and those that do not run in a container (kubelet and docker). On machines with systemd, the kubelet and container runtime write to `journald` or `/var/log` directory. They use the [klog](https://github.com/kubernetes/klog) logging library. 

**Cluster-level logging architectures**

- Use a node-level logging agent that runs on every node.

![Using a node level logging agent](.012-cluster-administration-images/logging-with-node-agent.png)

Because the logging agent must run on every node, it’s common to implement it as either a DaemonSet replica. Using a node-level logging agent is the most common and encouraged approach for a Kubernetes cluster. Two optional logging agents are packaged with the Kubernetes release: [Stackdriver Logging](https://kubernetes.io/docs/user-guide/logging/stackdriver) for use with Google Cloud Platform, and [Elasticsearch](https://kubernetes.io/docs/user-guide/logging/elasticsearch). 

- Include a dedicated sidecar container for logging in an application pod.

![Sidecar container with a streaming container](.012-cluster-administration-images/logging-with-streaming-sidecar.png)

The sidecar containers read logs from a file, a socket, or the journald. Each individual sidecar container prints log to its own `stdout` or `stderr` stream.

Note, that despite low CPU and memory usage (order of couple of millicores for cpu and order of several megabytes for memory), writing logs to a file and then streaming them to `stdout` can double disk usage.

![Sidecar container with a logging agent](.012-cluster-administration-images/logging-with-sidecar-agent.png)

Using a logging agent in a sidecar container can lead to significant resource consumption. Moreover, you won’t be able to access those logs using `kubectl logs` command, because they are not controlled by the kubelet. As an example, you could use [Stackdriver](https://kubernetes.io/docs/tasks/debug-application-cluster/logging-stackdriver/), which uses fluentd as a logging agent.

- Push logs directly to a backend from within an application.

![Exposing logs directly from the application](.012-cluster-administration-images/logging-from-application.png)

You can implement cluster-level logging by exposing or pushing logs directly from every application.


## Metrics For The Kubernetes Control Plane

Metrics in Kubernetes control plane are emitted in [prometheus format](https://prometheus.io/docs/instrumenting/exposition_formats/) and are human readable.

In most cases metrics are available on `/metrics` endpoint of the HTTP server. For components that doesn’t expose endpoint by default it can be enabled using `--bind-address` flag. [kubelet](https://kubernetes.io/docs/reference/generated/kubelet) also exposes metrics in `/metrics/cadvisor`, `/metrics/resource` and `/metrics/probes` endpoints.

Metric lifecycle: Alpha metric → Stable metric → Deprecated metric → Hidden metric → Deletion.

Admins can enable hidden metrics through a command-line flag on a  specific binary. This intends to be used as an escape hatch for admins  if they missed the migration of the metrics deprecated in the last  release.


## Configuring kubelet Garbage Collection

Garbage collection is a helpful function of kubelet that will clean  up unused images and unused containers. Kubelet will perform garbage  collection for containers every minute and garbage collection for images every five minutes.

External garbage collection tools are not  recommended as these tools can potentially break the behavior of kubelet by removing containers expected to exist.

---

Kubernetes manages lifecycle of all images through imageManager, with the cooperation of cadvisor.

The policy for garbage collecting images takes two factors into consideration: `HighThresholdPercent` and `LowThresholdPercent`. Disk usage above the high threshold will trigger garbage collection. The garbage collection will delete least recently used images until the low threshold has been met.

---

The policy for garbage collecting containers considers three user-defined variables. `MinAge` is the minimum age at which a container can be garbage collected. `MaxPerPodContainer` is the maximum number of dead containers every single pod (UID, container name) pair is allowed to have. `MaxContainers` is the maximum number of total dead containers. These variables can be individually disabled by setting `MinAge` to zero and setting `MaxPerPodContainer` and `MaxContainers` respectively to less than zero. 

Kubelet will act on containers that are unidentified, deleted, or  outside of the boundaries set by the previously mentioned flags.
