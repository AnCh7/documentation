## Overview

Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services.

**Traditional deployment era:** Applications run on physical servers. There was no way to define resource boundaries for applications in a physical  server, and this caused resource allocation issues.

**Virtualized deployment era:** Allows you to run multiple Virtual  Machines (VMs) on a single physical server’s CPU. Virtualization allows  applications to be isolated between VMs.

**Container deployment era:** Containers are similar to  VMs, but they have relaxed isolation properties to share the Operating  System (OS) among the applications. Therefore, containers are considered lightweight.

![Deployment evolution](.001-overview-images/container_evolution.svg)

Kubernetes provides you with:
- Service discovery and load balancing
- Storage orchestration
- Automated rollouts and rollbacks
- Automatic bin packing
- Self-healing
- Secret and configuration management

Kubernetes is `not` a traditional, all-inclusive PaaS (Platform as a Service) system:
* Does not limit the types of applications supported.
* Does not deploy source code and does not build your application.
* Does not provide application-level services, such as middleware (for example, message buses), data-processing frameworks, databases, caches, nor cluster storage systems as built-in services.
* Does not dictate logging, monitoring, or alerting solutions.
* Does not provide nor mandate a configuration language/system. 
* Does not provide nor adopt any comprehensive machine configuration, maintenance, management, or self-healing systems.


## Kubernetes Components

![Components of Kubernetes](.001-overview-images/components-of-kubernetes.png)

### Kubernetes Control Plane

The Control Plane governs how Kubernetes communicates with your cluster and maintains a record of all of the Kubernetes Objects in the system, and runs continuous control loops to manage those objects’ state. It responds to changes in the cluster and work to make the actual state of all the objects in the system match the desired state that you provided. The Control Plane uses the Pod Lifecycle Event Generator (PLEG) - processes running on your cluster.

#### Kubernetes Master

When you interact with Kubernetes you’re communicating with your cluster’s Kubernetes master.

**kube-apiserver**

The API server is a component of the Kubernetes [control plane](https://kubernetes.io/docs/reference/glossary/?all=true#term-control-plane) that exposes the Kubernetes API. The main implementation of a Kubernetes API server is [kube-apiserver](https://kubernetes.io/docs/reference/generated/kube-apiserver/).

**etcd**

Consistent and highly-available key value store used as Kubernetes’ backing store for all cluster data.

**kube-scheduler**

Control Plane component that watches for newly created pods with no assigned node, and selects a node for them to run on. Factors taken into account for scheduling decisions include individual and  collective resource requirements, hardware/software/policy constraints,  affinity and anti-affinity specifications, data locality, inter-workload interference and deadlines.

**kube-controller-manager**

Control Plane component that runs [controller](https://kubernetes.io/docs/concepts/architecture/controller/) processes:
- Node Controller: Responsible for noticing and responding when nodes go down.
- Replication Controller: Responsible for maintaining the correct number of pods for every replication controller object in the system.
- Endpoints Controller: Populates the Endpoints object (that is, joins Services & Pods).
- Service Account & Token Controllers: Create default accounts and API access tokens for new namespaces.

**cloud-controller-manager**

[cloud-controller-manager](https://kubernetes.io/docs/tasks/administer-cluster/running-cloud-controller/) runs controllers that interact with the underlying cloud providers. The following controllers have cloud provider dependencies:
- Node Controller: For checking the cloud provider to determine if a node has been deleted in the cloud after it stops responding
- Route Controller: For setting up routes in the underlying cloud infrastructure
- Service Controller: For creating, updating and deleting cloud provider load balancers
- Volume Controller: For creating, attaching, and mounting volumes, and interacting with the cloud provider to orchestrate volumes

#### Kubernetes Nodes

**kubelet**

Communicates with the Kubernetes Master. An agent that runs on each node in the cluster. It makes sure that containers are running in a pod. The kubelet takes a set of PodSpecs that are provided through various  mechanisms and ensures that the containers described in those PodSpecs  are running and healthy.

**kube-proxy**

Reflects Kubernetes networking services on each node. [kube-proxy](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) is a network proxy that runs on each node in your cluster, implementing part of the Kubernetes [Service](https://kubernetes.io/docs/concepts/services-networking/service/) concept. kube-proxy maintains network rules on nodes. These network rules allow network communication to your Pods from network sessions inside or outside of your cluster. kube-proxy uses the operating system packet filtering layer if there is one and it’s available. Otherwise, kube-proxy forwards the traffic itself.

**Container Runtime**

The container runtime is the software that is responsible for running containers.

#### Addons

Addons use Kubernetes resources ([DaemonSet ](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset), [Deployment ](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/), etc) to implement cluster features. Because these are providing cluster-level features, namespaced resources for addons belong within the `kube-system` namespace.

**DNS**

Cluster DNS is a DNS server, in addition to the other DNS server(s) in your  environment, which serves DNS records for Kubernetes services. Containers started by Kubernetes automatically include this DNS server in their DNS searches.

**Web UI (Dashboard)**

[Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) is a general purpose, web-based UI for Kubernetes clusters. It allows  users to manage and troubleshoot applications running in the cluster, as well as the cluster itself.

**Container Resource Monitoring**

[Container Resource Monitoring](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-usage-monitoring/) records generic time-series metrics about containers in a central database, and provides a UI for browsing that data.

**Cluster-level Logging**

A [cluster-level logging](https://kubernetes.io/docs/concepts/cluster-administration/logging/) mechanism is responsible for saving container logs to a central log store with search/browsing interface.


## The Kubernetes API

To make it easier to eliminate fields or restructure resource representations, Kubernetes supports multiple API versions, each at a different API path, such as `/api/v1` or `/apis/extensions/v1beta1` (alpha, beta and stable levels).

To make it easier to extend the Kubernetes API use [*API groups*](https://git.k8s.io/community/contributors/design-proposals/api-machinery/api-group.md). The API group is specified in a REST path and in the `apiVersion` field of a serialized object. They can be enabled or disabled by setting `--runtime-config` on apiserver. 

Currently there are several API groups in use:
1. The core group (legacy group) is at the REST path `/api/v1` and uses `apiVersion: v1`.
2. The named groups are at REST path `/apis/$GROUP_NAME/$VERSION`, and use `apiVersion: $GROUP_NAME/$VERSION` (e.g. `apiVersion: batch/v1`).

There are two supported paths to extending the API with [custom resources](https://kubernetes.io/docs/concepts/api-extension/custom-resources/):
1. [CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/extend-api-custom-resource-definitions/) is for users with very basic CRUD needs.
2. Users needing the full set of Kubernetes API semantics can implement their own apiserver and use the [aggregator](https://kubernetes.io/docs/tasks/access-kubernetes-api/configure-aggregation-layer/) to make it seamless for clients.


## Working with Kubernetes Objects

Kubernetes contains a number of abstractions that represent the state of your system: deployed containerized applications and workloads, their associated network and disk resources, and other information about what your cluster is doing. 

The basic Kubernetes objects include:
- [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/)
- [Service](https://kubernetes.io/docs/concepts/services-networking/service/)
- [Volume](https://kubernetes.io/docs/concepts/storage/volumes/)
- [Namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)

Higher-level abstractions called Controllers. Controllers build upon the basic objects, and provide additional functionality and convenience features:
- [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
- [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)
- [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)
- [Job](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/)

Kubernetes Objects are persistent entities in the Kubernetes system. Kubernetes uses these entities to represent the state of your cluster. To work with Kubernetes objects–whether to create, modify, or delete them–you’ll need to use the [Kubernetes API](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). Most often, you provide the information to kubectl in a .yaml file. kubectl converts the information to JSON when making the API request.

Every Kubernetes object includes two nested object fields that govern the object’s configuration: the object *spec* and the object *status*. The *spec* describes your desired state for the object–the characteristics that you want the object to have. The *status* describes the *actual state* of the object, and is supplied and updated by the Kubernetes system.

In the `.yaml` file for the Kubernetes object you want to create, you’ll need to set values for the following `required`  fields:
- `apiVersion` - Which version of the Kubernetes API you’re using to create this object
- `kind` - What kind of object you want to create
- `metadata` - Data that helps uniquely identify the object, including a `name` string, `UID`, and optional `namespace`
- `spec` - What state you desire for the object (different for every Kubernetes object)

**Names**

Each object in your cluster has a [*Name*](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names) that is unique for that type of resource. Every Kubernetes object also has a [*UID*](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#uids) that is unique across your whole cluster.

For non-unique user-provided attributes, Kubernetes provides [labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) and [annotations](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/).

**Namespaces**

Kubernetes supports multiple virtual clusters backed by the same physical cluster. These virtual clusters are called namespaces.

Namespaces are intended for use in environments with many users spread across multiple teams, or projects (more than > 1x).

Namespaces provide a scope for names. Names of resources need to be unique within a namespace, but not across namespaces. Namespaces can not be nested inside one another and each Kubernetes resource can only be in one namespace.

Namespaces are a way to divide cluster resources between multiple users (via [resource quota](https://kubernetes.io/docs/concepts/policy/resource-quotas/)).

When you create a [Service](https://kubernetes.io/docs/user-guide/services), it creates a corresponding [DNS entry](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/). This entry is of the form `..svc.cluster.local`

Low-level resources, such as [nodes](https://kubernetes.io/docs/admin/node) and persistentVolumes, are not in any namespace.

**Labels and Selectors**

Labels are key/value pairs that are attached to objects. Labels are intended to be used to specify identifying attributes of  objects that are meaningful and relevant to users, but do not directly  imply semantics to the core system. Labels can be used to organize and to select subsets of objects. Labels  can be attached to objects at creation time and subsequently added and  modified at any time. Each object can have a set of key/value labels defined. Each Key must be unique for a given object.

Labels allow for efficient queries and watches and are ideal for use in  UIs and CLIs. Non-identifying information should be recorded using [annotations](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/).

Valid label keys have two segments: an optional prefix and name, separated by a slash (`/`).

Unlike [names and UIDs](https://kubernetes.io/docs/user-guide/identifiers), labels do not provide uniqueness. 

Via a label selector, the client/user can identify a set of objects. The label selector is the core grouping primitive in Kubernetes.

**Annotations**

You can use Kubernetes annotations to attach arbitrary non-identifying metadata to objects. 

**Field Selectors**

*Field selectors* let you [select Kubernetes resources](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects) based on the value of one or more resource fields. 

```bash
kubectl get pods --field-selector=status.phase!=Running,spec.restartPolicy=Always
```

Using unsupported field selectors produces an error. You can use the `=`, `==`, and `!=` operators. As with [label](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels) and other selectors, field selectors can be chained together as a comma-separated list.

**Recommended Labels**

The metadata is organized around the concept of an application. 

| Key                            | Description                                                  | Example            | Type   |
| ------------------------------ | ------------------------------------------------------------ | ------------------ | ------ |
| `app.kubernetes.io/name`       | The name of the application                                  | `mysql`            | string |
| `app.kubernetes.io/instance`   | A unique name identifying the instance of an application     | `wordpress-abcxzy` | string |
| `app.kubernetes.io/version`    | The current version of the application (e.g., a semantic version, revision hash, etc.) | `5.7.21`           | string |
| `app.kubernetes.io/component`  | The component within the architecture                        | `database`         | string |
| `app.kubernetes.io/part-of`    | The name of a higher level application this one is part of   | `wordpress`        | string |
| `app.kubernetes.io/managed-by` | The tool being used to manage the operation of an application | `helm`             | string |