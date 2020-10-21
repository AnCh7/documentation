## Nodes

A node is a worker machine in Kubernetes: **[container runtime](https://kubernetes.io/docs/concepts/overview/components/#container-runtime) + kubelet + kube-proxy.** 

#### Node Status

A node’s status contains the following information:
- [Addresses](https://kubernetes.io/docs/concepts/architecture/nodes/#addresses): The usage of these fields varies depending on your cloud provider or bare metal configuration.
- The `conditions` field describes the status of all `Running` nodes.
  If the Status of the Ready condition remains `Unknown` or `False` for longer than the `pod-eviction-timeout`, an argument is passed to the [kube-controller-manager](https://kubernetes.io/docs/admin/kube-controller-manager/) and all the Pods on the node are scheduled for deletion by the Node Controller. The default eviction timeout duration is **five minutes**.
- [Capacity and Allocatable](https://kubernetes.io/docs/concepts/architecture/nodes/#capacity): CPU, memory and the maximum number of pods that can be scheduled onto the node.
- [Info](https://kubernetes.io/docs/concepts/architecture/nodes/#info): kernel version, Kubernetes version (kubelet and kube-proxy version), Docker version (if used), and OS name.

Kubectl command:
```bash
kubectl describe node xxxxxxxxxxx
```

Kubernetes creates a node object internally (the representation), and validates the node by health checking based on the `metadata.name` field. If the node is valid it is eligible to run a pod. Otherwise, it is ignored for any cluster activity until it becomes valid. Currently, there are three components that interact with the Kubernetes node interface: node controller, kubelet, and kubectl.

**Node Controller**

The node controller is a Kubernetes master component which manages various aspects of nodes: 
* assigning a CIDR block to the node when it is registered.
* keeping the node controller’s internal list of nodes up to date with the cloud provider’s list of available machines.
* monitoring the nodes’ health. The node controller is responsible for updating the NodeReady condition of NodeStatus to ConditionUnknown when a node becomes unreachable.

**Heartbeats**

The kubelet is responsible for creating and updating the `NodeStatus` and a Lease object:
- The kubelet updates the `NodeStatus` either when there is change in status, or if there has been no update for a configured interval.
- The kubelet creates and then updates its Lease object every 10 seconds (the default update interval). Lease updates occur independently from the `NodeStatus` updates. Lease is a lightweight resource, which improves the performance of the node heartbeats as the cluster scales.

**Reliability**

The node controller looks at the state of all nodes in the cluster when making a decision about pod eviction. In most cases, node controller limits the eviction rate to `--node-eviction-rate` (default 0.1) per second, meaning it won’t evict pods from more than 1 node per 10 seconds. 

The node eviction behavior changes when a node in a given availability zone becomes unhealthy.

**Self-Registration of Nodes**

When the kubelet flag `--register-node` is true (the default), the kubelet will attempt to register itself with the API server.

**Manual Node Administration**

Marking a node as unschedulable prevents new pods from being scheduled to that node, but does not affect any existing pods on the node. This is useful as a preparatory step before a node reboot:
```shell
kubectl cordon $NODENAME
```

**Node capacity**

The capacity of the node (number of cpus and amount of memory) is part of the node object. Normally, nodes register themselves and report their capacity when creating the node object. The Kubernetes scheduler ensures that there are enough resources for all the pods on a node. It checks that the sum of the requests of containers on the node is no greater than the node capacity.


## Master-Node Communication

#### Cluster to Master

All communication paths from the cluster to the master terminate at the apiserver (none of the other master components are designed to expose remote services). In a typical deployment, the apiserver is configured to listen for remote connections on a secure HTTPS port (443) with one or more forms of client [authentication](https://kubernetes.io/docs/reference/access-authn-authz/authentication/) enabled. One or more forms of [authorization](https://kubernetes.io/docs/reference/access-authn-authz/authorization/) should be enabled, especially if [anonymous requests](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#anonymous-requests) or [service account tokens](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#service-account-tokens) are allowed.

Nodes should be provisioned with the public root certificate for the cluster such that they can connect securely to the apiserver along with valid client credentials.

Pods that wish to connect to the apiserver can do so securely by leveraging a service account so that Kubernetes will automatically inject the public root certificate and a valid bearer token into the pod when it is instantiated.

The master components also communicate with the cluster apiserver over the secure port.

#### Master to Cluster

* ApiServer to the kubelet process which runs on each node in the cluster. **Unsafe** to run over untrusted and/or public networks. The connections are used for:
  * Fetching logs for pods.
  * Attaching (through kubectl) to running pods.
  * Providing the kubelet’s port-forwarding functionality.
* ApiServer to any node, pod, or service through the apiserver’s proxy functionality. **Unsafe** to run over untrusted and/or public networks.

Kubernetes supports SSH tunnels to protect the Master -> Cluster communication paths. 


## Controllers

In Kubernetes, controllers are control loops that watch the state of your [cluster ](https://kubernetes.io/docs/reference/glossary/?all=true#term-cluster), then make or request changes where needed. Each controller tries to move the current cluster state closer to the desired state.

The [Job](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion) controller is an example of a Kubernetes built-in controller. Built-in controllers manage state by interacting with the cluster API server. Job is a Kubernetes resource that runs (creates or removes) a [Pod ](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/), or perhaps several Pods, to carry out a task and then stop.

Controllers that interact with external state find their desired state from the API server, then communicate directly with an external system to bring the current state closer in line.

Your cluster could be changing at any point as work happens and control loops automatically fix failures. This means that, potentially, your cluster never reaches a stable state.

As long as the controllers for your cluster are running and able to make useful changes, it doesn’t matter if the overall state is or is not stable.

Kubernetes uses lots of controllers that each manage a particular aspect of cluster state. Kubernetes comes with a set of built-in controllers that run inside the [kube-controller-manager ](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/). These built-in controllers provide important core behaviors. You can find controllers that run outside the control plane, to extend Kubernetes.


## Cloud Controller Manager

The cloud controller manager’s design is based on a plugin mechanism  that allows new cloud providers to integrate with Kubernetes easily.

![CCM Kube Arch](.002-cluster-architecture-images/post-ccm-arch.png)

The CCM breaks away some of the functionality of Kubernetes controller manager (KCM) and runs it as a separate process. Specifically, it breaks away those controllers in the KCM that are  cloud dependent. The KCM has the following cloud dependent controller  loops:
- Node controller
  The Node controller is responsible for initializing a node by obtaining information about the nodes running in the cluster from the  cloud provider. The node controller performs the following functions:
  1. Initialize a node with cloud specific zone/region labels.
  2. Initialize a node with cloud specific instance details, for example, type and size.
  3. Obtain the node’s network addresses and hostname.
  4. In case a node becomes unresponsive, check the cloud to see if the node has been deleted from the cloud. If the node has been deleted from the cloud, delete the Kubernetes Node object.
- Route controller
  The Route controller is responsible for configuring routes in the cloud appropriately so that containers on different nodes in the Kubernetes  cluster can communicate with each other.
- Service controller
  The Service controller is responsible for listening to service create, update and delete events. Based on the current state of the services in Kubernetes, it configures cloud load balancers (such as ELB , Google LB, or Oracle Cloud Infrastructure LB) to reflect the state of the  services in Kubernetes. Additionally, it ensures that service backends  for cloud load balancers are up to date.

The introduction of the CCM has moved this node initialization operation from the kubelet into the CCM. kubelet initializes a node without cloud-specific information. However,  it adds a taint to the newly created node that makes the node  unschedulable until the CCM initializes the node with cloud-specific  information. It then removes this taint.