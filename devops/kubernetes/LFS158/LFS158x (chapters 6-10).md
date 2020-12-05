### Chapter 6. Minikube

Minikube builds all its infrastructure as long as the Type-2 Hypervisor is installed on our workstation. Minikube invokes the Hypervisor to create a single VM which then hosts a single-node Kubernetes cluster:

- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)  is a binary used to access and manage any Kubernetes cluster.
- Type-2 Hypervisor
- VT-x/AMD-v virtualization must be enabled on the local workstation in BIOS


###  Chapter 7. Accessing Minikube

**kubectl** is the **Kubernetes** **Command Line Interface (CLI) client** to manage cluster resources and applications. It can be used standalone, or part of scripts and automation tools. Once all required credentials and cluster access points have been configured for **kubectl** it can be used remotely from anywhere to access a cluster. 

The **Kubernetes Dashboard** provides a **Web-Based User Interface (Web UI)** to interact with a Kubernetes cluster to manage resources and containerized applications. In one of the later chapters, we will be using it to deploy a containerized application.

Kubernetes has the **API server**, and operators/users connect to it from the external world to interact with the cluster:

- **Core Group (/api/v1)**This group includes objects such as Pods, Services, nodes, namespaces, configmaps, secrets, etc.
- **Named Group**This group includes objects in **/apis/$NAME/$VERSION** format. These different API versions imply different levels of stability and support:
  *Alpha level* - it may be dropped at any point in time, without notice. For example, **/apis/batch/v2alpha1**.
  *Beta level* - it is well-tested, but the semantics of objects may change in incompatible ways in a subsequent beta or stable release. For example, **/apis/certificates.k8s.io/v1beta1**.
  *Stable level* - appears in released software for many subsequent versions. For example, **/apis/networking.k8s.io/v1**.
- **System-wide**This group consists of system-wide API endpoints, like **/healthz**, **/logs**, **/metrics**, **/ui**, etc.

[Kubernetes Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) provides a web-based user interface for Kubernetes cluster management. To access the dashboard from Minikube, we can use the **minikube dashboard** command or issuing the command:

```bash
kubectl proxy
```

**kubectl** authenticates with the API server on the master node and makes the Dashboard available on a slightly different URL than the one earlier, this time through the proxy port 8001.

When not using the **kubectl proxy**, we need to authenticate to the API server when sending API requests. We can authenticate by providing a **Bearer Token** when issuing a **curl**, or by providing a set of **keys** and **certificates**.


### Chapter 8. Kubernetes Building Blocks

Kubernetes has a very rich object model, representing different persistent entities in the Kubernetes cluster. Those entities describe:
* What containerized applications we are running and on which node
* Application resource consumption
* Different policies attached to applications, like restart/upgrade policies, fault tolerance, etc.

With each object, we declare our intent or the desired state under the **spec** section. The Kubernetes system manages the **status** section for objects, where it records the actual state of the object.

Examples of Kubernetes objects are Pods, ReplicaSets, Deployments, Namespaces, etc.

##### Pods

It is the unit of deployment in Kubernetes, which represents a single instance of the application. A Pod is a logical collection of one or more containers, which:

- Are scheduled together on the same host with the Pod
- Share the same network namespace
- Have access to mount the same external storage (volumes).

<img src=".LFS158x (chapters 6-x)-images/Pods.png" alt="A pod is a collection of one or more containers" style="zoom:50%;" />

Pods are ephemeral in nature, and they do not have the capability to self-heal by themselves. That is the reason they are used with controllers which handle Pods' replication, fault tolerance, self-healing, etc.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.15.11
    ports:
    - containerPort: 80
```


##### Labels

[Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) are key-value pairs attached to Kubernetes objects.

Controllers use Labels to logically group together decoupled objects, rather than using objects' names or IDs.

<img src=".LFS158x (chapters 6-x)-images/Labels.png" alt="img" style="zoom:50%;" />

Controllers use [Label Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#label-selectors) to select a subset of objects. Kubernetes supports two types of Selectors:
- Equality-Based Selectors
Using the **=**, **==** (equals, used interchangeably), or **!=** (not equals) operators. 
- Set-Based Selectors
Using **in**, **notin** operators for Label values, and **exist/does not exist** operators.


##### ReplicationControllers

Is a controller that ensures a specified number of replicas of a Pod is running at any given time. 


##### ReplicaSets

ReplicaSets support both equality- and set-based selectors, whereas ReplicationControllers only support equality-based Selectors.

<img src=".LFS158x (chapters 6-x)-images/Replica_Set1.png" alt="ReplicaSet" style="zoom:50%;" />

ReplicaSets can be used independently as Pod controllers but they only offer a limited set of features. A set of complementary features are provided by Deployments, the recommended controllers for the orchestration of Pods.


##### Deployments

[Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) objects provide declarative updates to Pods and ReplicaSets. The DeploymentController is part of the master node's controller manager, and it ensures that the current state always matches the desired state. It allows for seamless application updates and downgrades through **rollouts** and **rollbacks**, and it directly manages its ReplicaSets for application scaling. 

<img src=".LFS158x (chapters 6-x)-images/Deployment_Updated.png" alt="Deployment" style="zoom:50%;" />

A **rolling update** is triggered when we update the Pods Template for a deployment. Operations like scaling or labeling the deployment do not trigger a rolling update, thus do not change the Revision number.

![Deployment - update](.LFS158x (chapters 6-x)-images/ReplikaSet_B.png)


##### Namespaces

If multiple users and teams use the same Kubernetes cluster we can partition the cluster into virtual sub-clusters using [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/). The names of the resources/objects created inside a Namespace are unique, but not across Namespaces in the cluster.

Four default namespaces:
**kube-system** - Kubernetes system, mostly the control plane agents.
**kube-public** -  special Namespace, which is unsecured and readable by anyone, used for special purposes such as exposing public (non-sensitive) information about the cluster. 
**kube-node-lease** - holds node lease objects used for node heartbeat data.
**default** - created by administrators and developers. 


### Chapter 9. Authentication, Authorization, Admission Control

##### Authentication

Each access request goes through the following three stages:
- Authentication
  Logs in a user.
- Authorization
  Authorizes the API requests added by the logged-in user.
- Admission Control
  Software modules that can modify or reject the requests based on some additional checks, like a pre-set **Quota**.

The following image depicts the above stages:

![Accessing the API](.LFS158x (chapters 6-x)-images/access-k8s.png)

Kubernetes does not have an object called *user*, nor does it store *usernames* or other related details in its object store.

Kubernetes has two kinds of users:
- **Normal Users**
  They are managed outside of the Kubernetes cluster via independent services like User/Client Certificates, a file listing usernames/passwords, Google accounts, etc.
- **Service Accounts**
  With Service Account users, in-cluster processes communicate with the API server to perform different operations. Most of the Service Account users are created automatically via the API server, but they can also be created manually. The Service Account users are tied to a given Namespace and mount the respective credentials to communicate with the API server as Secrets.

If properly configured, Kubernetes can also support **anonymous requests**, along with requests from Normal Users and Service Accounts. **User impersonation** is also supported for a user to be able to act as another user, a useful feature for administrators when troubleshooting authorization policies.

Kubernetes uses different [authentication modules](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#authentication-strategies):
- **Client Certificates**To enable client certificate authentication, we need to reference a file containing one or more certificate authorities by passing the **--client-ca-file=SOMEFILE** option to the API server. The certificate authorities mentioned in the file would validate the client certificates presented to the API server.
- **Static Token File**
We can pass a file containing pre-defined bearer tokens with the **--token-auth-file=SOMEFILE** option to the API server. Currently, these tokens would last indefinitely, and they cannot be changed without restarting the API server.
- **Bootstrap Tokens**
Mostly used for bootstrapping a new Kubernetes cluster.
- **Static Password File**
It is similar to *Static Token File.* We can pass a file containing basic authentication details with the **--basic-auth-file=SOMEFILE** option. These credentials would last indefinitely, and passwords cannot be changed without restarting the API server.
- **Service Account Tokens**
This is an automatically enabled authenticator that uses signed bearer tokens to verify the requests. These tokens get attached to Pods using the ServiceAccount Admission Controller, which allows in-cluster processes to talk to the API server.
- **OpenID Connect Tokens**
OpenID Connect helps us connect with OAuth2 providers, such as Azure Active Directory, Salesforce, Google, etc., to offload the authentication to external services.
- **Webhook Token Authentication**
With Webhook-based authentication, verification of bearer tokens can be offloaded to a remote service.
- **Authenticating Proxy**
If we want to program additional authentication logic, we can use an authenticating proxy. 

We can enable multiple authenticators, and the first module to successfully authenticate the request short-circuits the evaluation. In order to be successful, you should enable at least two methods: the service account tokens authenticator and one of the user authenticators.


##### Authorization

Some of the API request attributes that are reviewed by Kubernetes include user, group, extra, Resource or Namespace, to name a few. Next, these attributes are evaluated against policies.

Authorization modules:

- **Node Authorizer**
  Node authorization is a special-purpose authorization mode which specifically authorizes API requests made by kubelets. It authorizes the kubelet's read operations for services, endpoints, nodes, etc., and writes operations for nodes, pods, events, etc.

- **Attribute-Based Access Control (ABAC) Authorizer**
  With the ABAC authorizer, Kubernetes grants access to API requests, which combine policies with attributes.
```json
{
  "apiVersion": "abac.authorization.kubernetes.io/v1beta1",
  "kind": "Policy",
  "spec": {
    "user": "student",
    "namespace": "lfs158",
    "resource": "pods",
    "readonly": true
  }
}
```

- **Webhook Authorizer**
  With the Webhook authorizer, Kubernetes can offer authorization decisions to some third-party services, which would return *true* for successful authorization, and *false* for failure.

- **Role-Based Access Control (RBAC) Authorizer**
  Regulates the access to resources based on the roles of individual users. In Kubernetes, we can have different roles that can be attached to subjects like users, service accounts, etc. While creating the roles, we restrict resource access by specific operations, such as **create**, **get**, **update**, **patch**, etc. These operations are referred to as verbs.

  We can create two kinds of roles: 

  - With **Role**, we can grant access to resources within a specific Namespace.

    ```json
    kind: Role
    apiVersion: rbac.authorization.k8s.io/v1
    metadata:
      namespace: lfs158
      name: pod-reader
    rules:
    - apiGroups: [""] # "" indicates the core API group
      resources: ["pods"]
      verbs: ["get", "watch", "list"]
    ```

  - The **ClusterRole** can be used to grant the same permissions as Role does, but its scope is cluster-wide.


**RoleBindings**:

* RoleBinding
  It allows us to bind users to the same namespace as a Role. We could also refer a ClusterRole in RoleBinding, which would grant permissions to Namespace resources defined in the ClusterRole within the RoleBinding’s Namespace.

  ```yaml
  kind: RoleBinding
  apiVersion: rbac.authorization.k8s.io/v1
  metadata:
    name: pod-read-access
    namespace: lfs158
  subjects:
  - kind: User
    name: student
    apiGroup: rbac.authorization.k8s.io
  roleRef:
    kind: Role
    name: pod-reader
    apiGroup: rbac.authorization.k8s.io
  ```

* ClusterRoleBinding
  It allows us to grant access to resources at a cluster-level and to all Namespaces.


##### Admission Control

Used to specify granular access control policies, which include allowing privileged containers, checking on resource quota, etc. They come into effect only after API requests are authenticated and authorized.

Kubernetes has some admission controllers enabled by default. For more details, please review the [Kubernetes documentation](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/).


### Chapter 10. Services

To access the application, a user/client needs to connect to the Pods. As Pods are ephemeral in nature, resources like IP addresses allocated to it cannot be static. Pods could be terminated abruptly or be rescheduled based on existing requirements. To overcome this situation, Kubernetes provides a higher-level abstraction called [Service](https://kubernetes.io/docs/concepts/services-networking/service/), which logically groups Pods and defines a policy to access them. This grouping is achieved via **Labels** and **Selectors**. 

<img src=".LFS158x (chapters 6-x)-images/Services2.png" alt="Services" style="zoom:50%;" />

We assign a name to the logical grouping, referred to as a **Service**. In our example, we create two Services, **frontend-svc**, and **db-svc**, and they have the **app==frontend** and the **app==db** Selectors, respectively.

Services can expose single Pods, ReplicaSets, Deployments, DaemonSets, and StatefulSets. For example:

```yaml
kind: Service
apiVersion: v1
metadata:
  name: frontend-svc
spec:
  selector:
    app: frontend
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5000
```

A Service provides load balancing by default while selecting the Pods for traffic forwarding.

All worker nodes run a daemon called **kube-proxy**, which watches the API server on the master node for the addition and removal of Services and endpoints (configures **iptables** rules).

![kube-proxy and services and endpoints](.LFS158x (chapters 6-x)-images/kubeproxy.png)

Kubernetes supports two methods for discovering Services:

* **Environment Variables**

  As soon as the Pod starts on any worker node, the **kubelet** daemon running on that node adds a set of environment variables in the Pod for all active Services.

* **DNS** (recommended)

  Kubernetes has an [add-on](https://github.com/kubernetes/kubernetes/blob/master/cluster/addons/README.md) for [DNS](https://github.com/kubernetes/kubernetes/tree/master/cluster/addons/dns), which creates a DNS record for each Service and its format is **my-svc.my-namespace.svc.cluster.local**. Services within the same Namespace find other Services just by their name. Pods from other Namespaces lookup the same Service by adding the respective Namespace as a suffix, such as **redis-master.my-ns**. 

Service can has different **access scope (ServiceType):**

* only accessible within the cluster
* accessible from within the cluster and the external world
* maps to an entity which resides either inside or outside the cluster


**Types:**

**ClusterIP** is the default ServiceType. A Service receives a Virtual IP address, known as its ClusterIP. This Virtual IP address is used for communicating with the Service and is accessible only within the cluster. 

With the **NodePort** ServiceType, in addition to a ClusterIP, a high-port, dynamically picked from the default range **30000-32767**, is mapped to the respective Service, from all the worker nodes. Useful when we want to make our Services accessible from the external world. 

To access multiple applications from the external world, administrators can configure a reverse proxy - an ingress, and define rules that target Services within the cluster.

With the **LoadBalancer** ServiceType:

* NodePort and ClusterIP are automatically created, and the external load balancer will route to them
* The Service is exposed at a static port on each worker node
* The Service is exposed externally using the underlying cloud provider's load balancer feature.

The LoadBalancer ServiceType will only work if the underlying infrastructure supports the automatic creation of Load Balancers and have the respective support in Kubernetes, as is the case with the Google Cloud Platform and AWS. If no such feature is configured, the LoadBalancer IP address field is not populated, and the Service will work the same way as a NodePort type Service.

**ExternalName** is a special ServiceType, that has no Selectors and does not define any endpoints. When accessed within the cluster, it returns a **CNAME** record of an externally configured Service. The primary use case of this *ServiceType* is to make externally configured Services like **my-database.example.com** available to applications inside the cluster.

A Service can be mapped to an **ExternalIP** address if it can route to one or more of the worker nodes. Traffic that is ingressed into the cluster with the ExternalIP (as destination IP) on the Service port, gets routed to one of the Service endpoints. This type of service requires an external cloud provider such as Google Cloud Platform or AWS.

 ![ServiceType - ExternalIP](.LFS158x (chapters 6-x)-images/ExternalIP.png) 