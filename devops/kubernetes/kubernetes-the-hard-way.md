> References:
> https://github.com/kelseyhightower/kubernetes-the-hard-way/

**Prerequisites**

* Install the Google Cloud SDK
  https://cloud.google.com/sdk/docs/quickstart-macos

* Create new project:
  ```bash
  # 1) Enable billing https://console.developers.google.com/project/PROJECT_ID/settings
  # 2) Enable Compute Engine API:
  gcloud services enable compute.googleapis.com
  ```

##### Installing the Client Tools

* The `cfssl` and `cfssljson` command line utilities will be used to provision a [PKI Infrastructure](https://en.wikipedia.org/wiki/Public_key_infrastructure) and generate TLS certificates.

```
brew install cfssl
```
* The `kubectl` command line utility is used to interact with the Kubernetes API Server.

**Provisioning Compute Resources**

Kubernetes requires a set of machines to host the Kubernetes control plane and the worker nodes where containers are ultimately run.

The Kubernetes [networking model](https://kubernetes.io/docs/concepts/cluster-administration/networking/#kubernetes-model) assumes a flat network in which containers and nodes can communicate with each other. In cases where this is not desired [network policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/) can limit how groups of containers are allowed to communicate with each other and external network endpoints.

* Create Virtual Private Cloud Network

  A dedicated [Virtual Private Cloud](https://cloud.google.com/compute/docs/networks-and-firewalls#networks) (VPC) network will be setup to host the Kubernetes cluster.

  A [subnet](https://cloud.google.com/compute/docs/vpc/#vpc_networks_and_subnets) must be provisioned with an IP address range large enough to assign a private IP address to each node in the Kubernetes cluster.

* Firewall Rules

  Create a firewall rule that allows internal communication across all protocols and allows external SSH, ICMP, and HTTPS.

* Kubernetes Public IP Address.

  Allocate a static IP address that will be attached to the external load balancer fronting the Kubernetes API Servers.

* Compute Instances

  Will be provisioned using [Ubuntu Server](https://www.ubuntu.com/server) 18.04, which has good support for the [containerd container runtime](https://github.com/containerd/containerd). Each compute instance will be provisioned with a fixed private IP address to simplify the Kubernetes bootstrapping process.

* Kubernetes Controllers

  Creates three compute instances which will host the Kubernetes control plane.

* Kubernetes Workers

  Each worker instance requires a pod subnet allocation from the Kubernetes cluster CIDR range. The `pod-cidr` instance metadata will be used to expose pod subnet allocations to compute instances at runtime.

* Configuring SSH Access

  SSH will be used to configure the controller and worker instances. When connecting to compute instances for the first time SSH keys will be generated for you and stored in the project or instance metadata as describe in the [connecting to instances](https://cloud.google.com/compute/docs/instances/connecting-to-instance) documentation.



**Provisioning a CA and Generating TLS Certificates**

Provision a [PKI Infrastructure](https://en.wikipedia.org/wiki/Public_key_infrastructure) using CloudFlare's PKI toolkit, [cfssl](https://github.com/cloudflare/cfssl), then use it to bootstrap a Certificate Authority, and generate TLS certificates for the following components: etcd, kube-apiserver, kube-controller-manager, kube-scheduler, kubelet, and kube-proxy.

* Certificate Authority

  Provision a Certificate Authority that can be used to generate additional TLS certificates.

* Client and Server Certificates

  * Generate the `admin` client certificate and private key

  * The Kubelet Client Certificates

    Kubernetes uses a [special-purpose authorization mode](https://kubernetes.io/docs/admin/authorization/node/) called Node Authorizer, that specifically authorizes API requests made by [Kubelets](https://kubernetes.io/docs/concepts/overview/components/#kubelet). In order to be authorized by the Node Authorizer, Kubelets must use a credential that identifies them as being in the `system:nodes` group, with a username of `system:node:<nodeName>`. In this section you will create a certificate for each Kubernetes worker node that meets the Node Authorizer requirements.

  * The Controller Manager Client Certificate

    Generate the kube-controller-manager client certificate and private key.

  * The Kube Proxy Client Certificate

    Generate the kube-proxy client certificate and private key.

  * The Scheduler Client Certificate

    Generate the kube-scheduler client certificate and private key.

  * The Kubernetes API Server Certificate

    The `kubernetes-the-hard-way` static IP address will be included in the list of subject alternative names for the Kubernetes API Server certificate. This will ensure the certificate can be validated by remote clients.

  * The Service Account Key Pair

    The Kubernetes Controller Manager leverages a key pair to generate and sign service account tokens as describe in the [managing service accounts](https://kubernetes.io/docs/admin/service-accounts-admin/) documentation.

  * Distribute the Client and Server Certificates

    Copy the appropriate certificates and private keys to each worker, controller instances.



**Generating Kubernetes Configuration Files for Authentication**

Generates [Kubernetes configuration files](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), also known as kubeconfigs, which enable Kubernetes clients to locate and authenticate to the Kubernetes API Servers.

* Client Authentication Configs

  * Kubernetes Public IP Address

    Each kubeconfig requires a Kubernetes API Server to connect to. To support high availability the IP address assigned to the external load balancer fronting the Kubernetes API Servers will be used.

  * The kubelet Kubernetes Configuration File

    When generating kubeconfig files for Kubelets the client certificate matching the Kubelet's node name must be used. This will ensure Kubelets are properly authorized by the Kubernetes [Node Authorizer](https://kubernetes.io/docs/admin/authorization/node/).

  * The kube-proxy Kubernetes Configuration File - generate a kubeconfig file for the `kube-proxy` service.

  * The kube-controller-manager Kubernetes Configuration File - generate a kubeconfig file for the `kube-controller-manager` service.

  * The kube-scheduler Kubernetes Configuration File - generate a kubeconfig file for the `kube-scheduler` service.

  * The admin Kubernetes Configuration File - generate a kubeconfig file for the `admin` user.

* Distribute the Kubernetes Configuration Files

  Copy the appropriate `kubelet` and `kube-proxy` kubeconfig files to each worker instance and

  copy the appropriate `kube-controller-manager` and `kube-scheduler` kubeconfig files to each controller instance.



**Generating the Data Encryption Config and Key**

Kubernetes stores a variety of data including cluster state, application configurations, and secrets. Kubernetes supports the ability to [encrypt](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data) cluster data at rest.

* Generate an encryption key
* Create the encryption-config.yaml encryption config file
* Copy the encryption-config.yaml encryption config file to each controller instance.



**Bootstrapping the etcd Cluster**

Kubernetes components are stateless and store cluster state in [etcd](https://github.com/coreos/etcd).

* Download and Install the etcd Binaries

* Configure the etcd Server

  The instance internal IP address will be used to serve client requests and communicate with etcd cluster peers. 

  Each etcd member must have a unique name within an etcd cluster. 

  Create the `etcd.service` systemd unit file.

* Start the etcd Server

* Verification

  

**Bootstrapping the Kubernetes Control Plane**

Configure kubernetes control plane across three compute instances for high availability. Create an external load balancer that exposes the Kubernetes API Servers to remote clients. Install on each node: Kubernetes API Server, Scheduler, and Controller Manager.

* Provision the Kubernetes Control Plane

* Download and Install the Kubernetes Controller Binaries

* Configure the Kubernetes API Server

  The instance internal IP address will be used to advertise the API Server to members of the cluster.

  Create the `kube-apiserver.service` systemd unit file.

* Configure the Kubernetes Controller Manager

* Configure the Kubernetes Scheduler

* Start the Controller Services

* Enable HTTP Health Checks

  A [Google Network Load Balancer](https://cloud.google.com/compute/docs/load-balancing/network) will be used to distribute traffic across the three API servers and allow each API server to terminate TLS connections and validate client certificates. The network load balancer only supports HTTP health checks which means the HTTPS endpoint exposed by the API server cannot be used. As a workaround the nginx webserver can be used to proxy HTTP health checks.

* RBAC for Kubelet Authorization

  Configure RBAC permissions to allow the Kubernetes API Server to access the Kubelet API on each worker node. Access to the Kubelet API is required for retrieving metrics, logs, and executing commands in pods.

  Create the `system:kube-apiserver-to-kubelet` [ClusterRole](https://kubernetes.io/docs/admin/authorization/rbac/#role-and-clusterrole) with permissions to access the Kubelet API and perform most common tasks associated with managing pods.

  The Kubernetes API Server authenticates to the Kubelet as the `kubernetes` user using the client certificate as defined by the `--kubelet-client-certificate` flag.

  Bind the `system:kube-apiserver-to-kubelet` ClusterRole to the `kubernetes` user.

* The Kubernetes Frontend Load Balancer

  Provision an external load balancer to front the Kubernetes API Servers. The `kubernetes-the-hard-way` static IP address will be attached to the resulting load balancer.

  * Provision a Network Load Balancer



**Bootstrapping the Kubernetes Worker Nodes**

Bootstrap three Kubernetes worker nodes. The following components will be installed on each node: [runc](https://github.com/opencontainers/runc), [gVisor](https://github.com/google/gvisor), [container networking plugins](https://github.com/containernetworking/cni), [containerd](https://github.com/containerd/containerd), [kubelet](https://kubernetes.io/docs/admin/kubelet), and [kube-proxy](https://kubernetes.io/docs/concepts/cluster-administration/proxies).

* Download and Install Worker Binaries
* Configure CNI Networking
* Configure containerd
* Configure the Kubelet
* Configure the Kubernetes Proxy
* Start the Worker Services



**Configuring kubectl for Remote Access**

Generate a kubeconfig file for the kubectl command line utility based on the admin user credentials.

Each kubeconfig requires a Kubernetes API Server to connect to. To support high availability the IP address assigned to the external load balancer fronting the Kubernetes API Servers will be used.



**Provisioning Pod Network Routes**

Pods scheduled to a node receive an IP address from the node's Pod CIDR range. At this point pods can not communicate with other pods running on different nodes due to missing network [routes](https://cloud.google.com/compute/docs/vpc/routes).

Creates a route for each worker node that maps the node's Pod CIDR range to the node's internal IP address:

* gather the information required to create routes

* create network routes for each worker instance

  

**Deploying the DNS Cluster Add-on**

Deploy the [DNS add-on](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/) which provides DNS based service discovery, backed by [CoreDNS](https://coredns.io/), to applications running inside the Kubernetes cluster.



**Smoke Test**

Ensure your Kubernetes cluster is functioning correctly.



**Cleaning Up**

Delete the compute resources created during this tutorial.



*Compute Instances*

Delete the controller and worker compute instances:

```bash
gcloud -q compute instances delete \
  controller-0 controller-1 controller-2 \
  worker-0 worker-1 worker-2
```



*Networking*

Delete the external load balancer network resources:

```bash
gcloud -q compute forwarding-rules delete kubernetes-forwarding-rule \
    --region $(gcloud config get-value compute/region)
  gcloud -q compute target-pools delete kubernetes-target-pool
  gcloud -q compute http-health-checks delete kubernetes
  gcloud -q compute addresses delete kubernetes-the-hard-way
```

Delete the `kubernetes-the-hard-way` firewall rules:

```bash
gcloud -q compute firewall-rules delete \
  kubernetes-the-hard-way-allow-nginx-service \
  kubernetes-the-hard-way-allow-internal \
  kubernetes-the-hard-way-allow-external \
  kubernetes-the-hard-way-allow-health-check
```

Delete the `kubernetes-the-hard-way` network VPC:

```bash
gcloud -q compute routes delete \
    kubernetes-route-10-200-0-0-24 \
    kubernetes-route-10-200-1-0-24 \
    kubernetes-route-10-200-2-0-24

  gcloud -q compute networks subnets delete kubernetes
  gcloud -q compute networks delete kubernetes-the-hard-way
```
