### Chapter 1. From Monolith to Microservices

A **monolith** disadvantages: 

- has a rather expensive taste in **hardware**. The hardware of such capacity is both complex and pricey.
- **scaling** of individual features of the monolith is almost impossible. However, scaling the entire application means to manually deploy a new instance of the monolith on another server, typically behind a load balancing appliance - another pricey solution.
- **downtimes** occur and maintenance windows have to be planned as disruptions in service are expected to impact clients.

**Microservices** advantages: 

- can be **deployed individually** on separate servers provisioned with fewer resources.
- Microservices-based architecture is aligned with Event-driven Architecture and Service-Oriented Architecture (**SOA**) principles.
- Each microservice is developed and written in a modern programming language, selected to be the **best suitable** for the type of service and its business function. This offers a great deal of flexibility.
- **scalability** - manually or automated through demand-based autoscaling.
- **Seamless upgrades and patching processes** - as a result, businesses are able to develop and roll-out new features and updates a lot faster, in an agile approach, having separate teams focusing on separate features, thus being more productive and cost-effective.
- An incremental **refactoring** approach guarantees that new features are developed and implemented as modern microservices which are able to communicate with the monolith through APIs, without appending to the monolith's code.

The refactoring path from a monolith to microservices is not smooth and without challenges. Not all monoliths are perfect candidates for refactoring, while some may not even "survive" such a modernization phase. When deciding whether a monolith is a possible candidate for refactoring, there are many possible issues to consider:

- programming languages - it may be more economical to just re-build it from the ground up as a cloud-native application.
- poorly designed legacy application should be re-designed and re-built from scratch following modern architectural patterns for microservices and even containers.
- tightly coupled with data stores are also poor candidates for refactoring.
- design mechanisms or find suitable tools to keep alive all the decoupled modules to ensure application resiliency as a whole. 
- chosing runtimes - containers came along, providing encapsulated lightweight runtime environments for application modules.

### Chapter 2. Container Orchestration

**Containers** are application-centric methods to deliver high-performing, scalable applications on any infrastructure of your choice. Containers are best suited to deliver microservices by providing portable, isolated virtual environments for applications to run without interference from other running applications.

![Containers bundle applications and libraries](.LFS158x (chapters 1-5)-images/Ch_2_-_Containers_-_why_containers.png)

**Microservices** are lightweight applications written in various modern programming languages, with specific dependencies, libraries and environmental requirements. To ensure that an application has everything it needs to run successfully it is packaged together with its dependencies.

Containers encapsulate microservices and their dependencies but do not run them directly. Containers run container images.

A **container image** bundles the application along with its runtime and dependencies, and a container is deployed from the container image offering an isolated executable environment for the application. Containers can be deployed from a specific image on many platforms, such as workstations, Virtual Machines, public cloud, etc.

**Container orchestrators** are tools which group systems together to form clusters where containers' deployment and management is automated at scale while meeting the requirements like:

- Fault-tolerance
- On-demand scalability
- Optimal resource usage
- Auto-discovery to automatically discover and communicate with each other
- Accessibility from the outside world
- Seamless updates/rollbacks without any downtime.

Most container orchestrators can:

- Group hosts together while creating a cluster.
- Schedule containers to run on hosts in the cluster based on resources availability.
- Enable containers in a cluster to communicate with each other regardless of the host they are deployed to in the cluster.
- Bind containers and storage resources.
- Group sets of similar containers and bind them to load-balancing constructs to simplify access to containerized applications by creating a level of abstraction between the containers and the user.
- Manage and optimize resource usage.
- Allow for implementation of policies to secure access to applications running inside containers.

### Chapter 3. Kubernetes

Kubernetes offers a very rich set of features for container orchestration. Some of its fully supported features are:

- **Automatic bin packing**
  Kubernetes automatically schedules containers based on resource needs and constraints, to maximize utilization without sacrificing availability.
- **Self-healing**
  Kubernetes automatically replaces and reschedules containers from failed nodes. It kills and restarts containers unresponsive to health checks, based on existing rules/policy. It also prevents traffic from being routed to unresponsive containers.
- **Horizontal scaling**
  With Kubernetes applications are scaled manually or automatically based on CPU or custom metrics utilization.
- **Service discovery and Load balancing**
  Containers receive their own IP addresses from Kubernetes, white it assigns a single Domain Name System (DNS) name to a set of containers to aid in load-balancing requests across the containers of the set.
- **Automated rollouts and rollbacks**
  Kubernetes seamlessly rolls out and rolls back application updates and configuration changes, constantly monitoring the application's health to prevent any downtime.
- **Secret and configuration management**
  Kubernetes manages secrets and configuration details for an application separately from the container image, in order to avoid a re-build of the respective image. Secrets consist of confidential information passed to the application without revealing the sensitive content to the stack configuration, like on GitHub.
- **Storage orchestration**
  Kubernetes automatically mounts software-defined storage (SDS) solutions to containers from local storage, external cloud providers, or network storage systems.
- **Batch execution**
  Kubernetes supports batch execution, long-running jobs, and replaces failed containers.
- **Portable and extensible**
- **Modular and pluggable**
- **Kubernetes is supported by a thriving community across the world**

The [Cloud Native Computing Foundation](https://www.cncf.io/) (CNCF) is one of the projects hosted by [the Linux Foundation](https://www.linuxfoundation.org/). CNCF aims to accelerate the adoption of containers, microservices, and cloud-native applications.

![CNCF logo](.LFS158x (chapters 1-5)-images/logo_cncf.png)

Graduated projects:

- [Kubernetes](https://kubernetes.io/) for container orchestration
- [Prometheus](https://prometheus.io/) for monitoring
- [Envoy](https://github.com/envoyproxy/envoy) for service mesh
- [CoreDNS](https://coredns.io/) for service discovery
- [containerd](http://containerd.io/) for container runtime
- [Fluentd](http://www.fluentd.org/) for logging

Incubating projects:

- [rkt](https://github.com/rkt/rkt) and [CRI-O](https://cri-o.io/) for container runtime
- [Linkerd](https://linkerd.io/) for service mesh
- [etcd](https://github.com/etcd-io) for key/value store
- [gRPC](http://www.grpc.io/) for remote procedure call (RPC)
- [CNI](https://github.com/containernetworking/cni) for networking API
- [Harbor](https://goharbor.io/) for registry
- [Helm](https://www.helm.sh/) for package management
- [Rook](https://github.com/rook/rook) and [Vitess](http://vitess.io/) for cloud-native storage
- [Notary](https://github.com/theupdateframework/notary) for security
- [TUF](https://github.com/theupdateframework/specification) for software updates
- [NATS](https://nats.io/) for messaging
- [Jaeger](https://github.com/jaegertracing/jaeger) and [OpenTracing](http://opentracing.io/) for distributed tracing
- [Open Policy Agent](https://www.openpolicyagent.org/) for policy.

### Chapter 4. Kubernetes Architecture

At a very high level, Kubernetes has the following main components:

- One or more **master nodes**
- One or more **worker nodes**
- Distributed key-value store, such as **etcd**.

![Kubernetes Architecture](.LFS158x (chapters 1-5)-images/Kubernetes_Architecture1.png)

The **master node** provides a running environment for the control plane responsible for managing the state of a Kubernetes cluster, and it is the brain behind all operations inside the cluster. The control plane components are agents with very distinct roles in the cluster's management. In order to communicate with the Kubernetes cluster, users send 
requests to the master node via:

- Command Line Interface (CLI) tool
- Web User-Interface (Web UI) Dashboard
- Application Programming Interface (API)

![Kubernetes Master Node](.LFS158x (chapters 1-5)-images/Master_Node1.png) 

To ensure the control plane's fault tolerance, master node replicas are added to the cluster, configured in High-Availability (HA) mode. While only one of the master node replicas actively manages the cluster, the control plane components stay in sync across the master node replicas. This type of configuration adds resiliency to the cluster's control plane, should the active master node replica fail.

To persist the Kubernetes cluster's state, all cluster configuration data is saved to [etcd](https://github.com/etcd-io). etcd is configured on the master node ([stacked](https://kubernetes.io/docs/setup/independent/ha-topology/#stacked-etcd-topology)) or on its dedicated host ([external](https://kubernetes.io/docs/setup/independent/ha-topology/#external-etcd-topology)) to reduce the chances of data store loss by decoupling it from the control plane agents.

When stacked, HA master node replicas ensure etcd resiliency as well. Unfortunately, that is not the case of external etcds, when the etcd hosts have to be separately replicated for HA mode configuration.

A **master node** has the following components:

- **API server** - administrative tasks.
  The API server intercepts RESTful calls from users, operators and external agents, then validates and processes them. During processing the API server reads the Kubernetes cluster's current state from the etcd, and after a call's execution, the resulting state of the Kubernetes cluster is saved in the distributed key-value data store for persistence. The API server is the only master plane component to talk to the etcd data store, both to read and to save Kubernetes cluster state information from/to it - acting as a middle-man interface for any other control plane agent requiring to access the cluster's data store.
- **Scheduler** - assign new objects, such as pods, to nodes.
  The scheduler obtains from etcd, via the API server, resource usage data for each worker node in the cluster. The scheduler also receives from the API server the new object's requirements which are part of its configuration data. Requirements may include constraints that users and operators set, such as scheduling work on a node labeled with **disk == ssd** key/value pair. The scheduler also takes into account Quality of Service (QoS) requirements, data locality, affinity, anti-affinity, taints, toleration, etc. Additional custom schedulers are supported.
- **Controller managers** - regulate the state of the Kubernetes cluster.
  Controllers are watch-loops continuously running and comparing the cluster's desired state (provided by objects' configuration data) with its current state (obtained from etcd data store via the API server). In case of a mismatch corrective action is taken in the cluster until its current state matches the desired state.
  The **kube-controller-manager** runs controllers responsible to act when nodes become unavailable, to ensure pod counts are as expected, to create endpoints, service accounts, and API access tokens.
  The **cloud-controller-manager** runs controllers responsible to interact with the underlying infrastructure of a cloud provider when nodes become unavailable, to manage storage volumes when provided by a cloud service, and to manage load balancing and routing.
- **etcd** - distributed key-value data store used to persist a Kubernetes cluster's state.
  New data is written to the data store only by appending to it, data is never replaced in the data store. Obsolete data is compacted periodically to minimize the size of the data store. etcd's CLI management tool provides backup, snapshot, and restore capabilities. 
  Both stacked and external etcd configurations support HA configurations.

A **worker node** provides a running environment for client applications. 

These applications are encapsulated in Pods, controlled by the cluster control plane agents running on the master node. Pods are scheduled on worker nodes, where they find required compute, memory and storage resources to run, and networking to talk to each other and the outside world.

![Worker Node](.LFS158x (chapters 1-5)-images/Worker_Node.png)

A worker node has the following components:

- **Container runtime** - supports docker, rkt, containerd, etc.
- **kubelet** - agent running on each node.
  It receives Pod definitions, primarily from the API server, and interacts with the container runtime on the node to run containers associated with the Pod. It also monitors the health of the Pod's running containers.
  The kubelet connects to the container runtime using **Container Runtime Interface (CRI)**. CRI consists of protocol buffers, gRPC API, and libraries. CRI implements two services: **ImageService** and **RuntimeService**. The **ImageService** is responsible for all the image-related operations, while the **RuntimeService** is responsible for all the Pod and container-related operations.

  ![Container Runtime Interface](.LFS158x (chapters 1-5)-images/CRI.png)

- **kube-proxy** - network agent which runs on each node responsible for dynamic updates and maintenance of all networking rules on the node. It abstracts the details of Pods networking and forwards connection requests to Pods.
- Addons for DNS, Dashboard, cluster-level monitoring and logging.
  - **DNS** - cluster DNS is a DNS server required to assign DNS records to Kubernetes objects and resources
  - **Dashboard** - a general purposed web-based user interface for cluster management
  - **Monitoring** - collects cluster-level container metrics and saves them to a central data store
  - **Logging** - collects cluster-level container logs and saves them to a central log store for analysis.

CRI shims:

- **dockershim**
  With dockershim, containers are created using Docker installed on the worker nodes. Internally, Docker uses containerd to create and manage containers.
  ![dockershim](.LFS158x (chapters 1-5)-images/dockershim.png)
- **cri-containerd**
  With cri-containerd, we can directly use Docker's smaller offspring containerd to create and manage containers.
  ![cri-containerd](.LFS158x (chapters 1-5)-images/cri-containerd.png)
- **CRI-O**
  CRI-O enables using any Open Container Initiative (OCI) compatible runtimes with Kubernetes, supported runC and Clear Containers as container runtimes.
  ![cri-o](.LFS158x (chapters 1-5)-images/crio.png)

##### Container-to-Container Communication Inside Pods

Making use of the underlying host operating system's kernel features, a container runtime creates an isolated network space for each container it starts. On Linux, that isolated network space is referred to as a **network namespace**. A network namespace is shared across containers, or with the host operating system.

When a Pod is started, a network namespace is created inside the Pod, and all containers running inside the Pod will share that network namespace so that they can talk to each other via localhost.

##### Pod-to-Pod Communication Across Nodes

In a Kubernetes cluster Pods are scheduled on nodes randomly. Regardless of their host node, Pods are expected to be able to communicate with all other Pods in the cluster, all this without the implementation of Network Address Translation (NAT). This is a fundamental requirement of any networking implementation in Kubernetes.

The Kubernetes network model aims to reduce complexity, and it treats Pods as VMs on a network, where each VM receives an IP address - thus each Pod receiving an IP address. This model is called "**IP-per-Pod**" and ensures Pod-to-Pod communication, just as VMs are able to communicate with each other.

Containers share the Pod's network namespace and must coordinate ports assignment inside the Pod just as applications would on a VM, all while being able to communicate with each other on **localhost** - inside the Pod. However, containers are integrated with the overall Kubernetes networking model through the use of the **Container Network Interface (CNI)** supported by **CNI plugins**. 

CNI is a set of a specification and libraries which allow plugins to configure the networking for containers. While there are a few [core plugins](https://github.com/containernetworking/plugins#plugins), most CNI plugins are 3rd-party Software Defined Networking (SDN) solutions implementing the Kubernetes networking model. In addition to addressing the fundamental requirement of the networking model, some networking solutions offer support for Network Policies. [Flannel](https://github.com/coreos/flannel/), [Weave](https://www.weave.works/oss/net/), [Calico](https://www.projectcalico.org/) are only a few of the SDN solutions available for Kubernetes clusters.

![Container Network Interface (.LFS158x (chapters 1-5)-images/Container_Network_Interface_CNI.png)](https://prod-edxapp.edx-cdn.org/assets/courseware/v1/e7f4f4c4b79ffd11fb57659d8558943b/asset-v1:LinuxFoundationX+LFS158x+2T2019+type@asset+block/Container_Network_Interface_CNI.png)

The container runtime offloads the IP assignment to CNI, which connects to the underlying configured plugin, such as Bridge or MACvlan, to get the IP address. Once the IP address is given by the respective plugin, CNI forwards it back to the requested container runtime.

##### Pod-to-External World Communication

Kubernetes enables external accessibility through **services**, complex constructs which encapsulate networking rules definitions on cluster nodes. By exposing services to the external world with **kube-proxy**, applications become accessible from outside the cluster over a virtual IP.

### Chapter 5. Installing Kubernetes

The four major installation types are briefly presented below:

- **All-in-One Single-Node Installation**
  In this setup, all the master and worker components are installed and running on a single-node. While it is useful for learning, development, and testing, and it should not be used in production. Minikube is one such example, and we are going to explore it in future chapters.
- **Single-Node etcd, Single-Master and Multi-Worker Installation**
  In this setup, we have a single-master node, which also runs a single-node etcd instance. Multiple worker nodes are connected to the master node.
- **Single-Node etcd, Multi-Master and Multi-Worker Installation**
  In this setup, we have multiple-master nodes configured in HA mode, but we have a single-node etcd instance. Multiple worker nodes are connected to the master nodes.
- **Multi-Node etcd, Multi-Master and Multi-Worker Installation**
  In this mode, etcd is configured in clustered HA mode, the master nodes are all configured in HA mode, connecting to multiple worker nodes. This is the most advanced and recommended production setup.

**Localhost Installation**

These are only a few localhost installation options available to deploy single- or multi-node Kubernetes clusters on our workstation/laptop:

- [Minikube](https://kubernetes.io/docs/getting-started-guides/minikube/) - single-node local Kubernetes cluster. Preferred.
- [Docker Desktop](https://www.docker.com/products/docker-desktop) - single-node local Kubernetes cluster for Windows and Mac
- [CDK on LXD](https://www.ubuntu.com/kubernetes/docs/install-local) - multi-node local cluster with LXD containers.

**On-Premise Installation**

- **On-Premise VMs**
  Kubernetes can be installed on VMs created via Vagrant, VMware vSphere, KVM, or another Configuration Management (CM) tool in conjunction with a hypervisor software. There are different tools available to automate the installation, such as [Ansible](https://www.ansible.com/) or [kubeadm](https://github.com/kubernetes/kubeadm).
- **On-Premise Bare Metal**
  Kubernetes can be installed on on-premise bare metal, on top of different operating systems, like RHEL, CoreOS, CentOS, Fedora, Ubuntu, etc. Most of the tools used to install Kubernetes on VMs can be used with bare metal installations as well.

**Cloud Installation**

Kubernetes can be installed and managed on almost any cloud environment:

- **Hosted Solutions**
  [Google Kubernetes Engine](https://cloud.google.com/kubernetes-engine/) (GKE)
  [Azure Kubernetes Service](https://azure.microsoft.com/en-us/services/kubernetes-service/) (AKS)
  [Amazon Elastic Container Service for Kubernetes](https://aws.amazon.com/eks/) (EKS)
  [DigitalOcean Kubernetes](https://www.digitalocean.com/products/kubernetes/)
  [OpenShift Dedicated](https://www.openshift.com/products/dedicated/)
  [Platform9](https://platform9.com/managed-kubernetes/)
  [IBM Cloud Kubernetes Service](https://cloud.ibm.com/docs/containers?topic=containers-getting-started#container_index).

- **Turnkey Cloud Solutions**
  [Google Compute Engine](https://kubernetes.io/docs/setup/turnkey/gce/) (GCE)
  [Amazon AWS](https://kubernetes.io/docs/setup/turnkey/aws/) (AWS EC2)
  [Microsoft Azure](https://kubernetes.io/docs/setup/turnkey/azure/) (AKS)

- **Turnkey On-Premise Solutions**
  [GKE On-Prem](https://cloud.google.com/gke-on-prem/) by Google Cloud
  [IBM Cloud Private](https://www.ibm.com/cloud/private)
  [OpenShift Container Platform](https://www.openshift.com/products/container-platform/) by Red Hat

While discussing installation configuration and the underlying infrastructure, let's take a look at some useful tools/resources available:

- **kubeadm**
  [kubeadm](https://github.com/kubernetes/kubeadm) is a first-class citizen on the Kubernetes ecosystem. It is a secure and recommended way to bootstrap a single- or multi-node Kubernetes cluster. It has a set of building blocks to setup the cluster, but it is easily extendable to add more features. Please note that kubeadm does not support the provisioning of hosts.
- **kubespray**
  With [kubespray](https://kubernetes.io/docs/setup/custom-cloud/kubespray/) (formerly known as kargo), we can install Highly Available Kubernetes clusters on AWS, GCE, Azure, OpenStack, or bare metal. Kubespray is based on Ansible, and is available on most Linux distributions.
- **kops**
  With [kops](https://kubernetes.io/docs/setup/custom-cloud/kops/), we can create, destroy, upgrade, and maintain production-grade, highly-available Kubernetes clusters from the command line. It can provision the machines as well. 
- **kube-aws**
  With [kube-aws](https://github.com/kubernetes-incubator/kube-aws) we can create, upgrade and destroy Kubernetes clusters on AWS from the command line.
