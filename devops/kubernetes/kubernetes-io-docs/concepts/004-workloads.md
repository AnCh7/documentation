## Pods

A Pod is the basic execution unit of a Kubernetes application–the smallest and simplest unit in the Kubernetes object  model that you create or deploy. A Pod represents processes running on your [Cluster ](https://kubernetes.io/docs/reference/glossary/?all=true#term-cluster). 

A Pod encapsulates an application’s container (multiple containers), storage resources, a unique network IP, and options that govern how the container(s) should run. A Pod represents a unit of deployment: a single instance of an application in Kubernetes, which might consist of either a single [container](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/#why-containers) or a small number of containers that are tightly coupled and that share resources. The shared context of a Pod is a set of Linux namespaces, cgroups, and potentially other facets of isolation - the same things that isolate a Docker container.

* **Pods that run a single container**.  Pod as a wrapper around a single  container, and Kubernetes manages the Pods rather than the containers  directly.

* **Pods that run multiple containers that need to work together**. A Pod might encapsulate an application composed of multiple co-located  containers that are tightly coupled and need to share resources.
  Pods provide two kinds of shared resources for their constituent containers: networking and storage:
  - Each Pod is assigned a unique IP address. Containers inside a Pod can communicate with one another using `localhost`. Containers in different Pods have distinct IP addresses and can not communicate by IPC without [special configuration](https://kubernetes.io/docs/concepts/policy/pod-security-policy/). These containers usually communicate with each other via Pod IP addresses.
  - They can also communicate with each other using standard inter-process communications like SystemV semaphores or POSIX shared memory. 
  - All containers in the Pod can access the shared volumes, allowing those  containers to share data. Volumes also allow persistent data in a Pod to survive in case one of the containers within needs to be restarted.

If you want to scale your application horizontally (replication), you should use multiple Pods, one for each instance. When a Pod gets created (directly by you, or indirectly by a Controller), it is scheduled to run on a [Node](https://kubernetes.io/docs/concepts/architecture/nodes/) in your cluster. The Pod remains on that Node until the process is terminated, the pod object is deleted, the Pod is evicted for lack of resources, or the Node fails.

Pods do not, by themselves, self-heal. Kubernetes uses a higher-level abstraction, called a Controller, that handles the work of managing the relatively disposable Pod instances. A Controller can create and manage multiple Pods for you, handling  replication and rollout and providing self-healing capabilities at  cluster scope. Controllers use a Pod Template that you provide to create the Pods for which it is responsible.

Like individual application containers, Pods are considered to be relatively **ephemeral** (rather than durable) entities. Pods are created, assigned a unique ID (UID), and scheduled to nodes where they remain until termination (according to restart policy) or deletion. If a [Node](https://kubernetes.io/docs/concepts/architecture/nodes/) dies, the Pods scheduled to that node are scheduled for deletion, after a timeout period. A given Pod (as defined by a UID) is not “rescheduled” to a new node; instead, it can be replaced by an identical Pod, with even the same name if desired, but with a new UID (see [replication controller](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/) for more details).

Pods serve as unit of deployment, horizontal scaling, and replication. Colocation (co-scheduling), shared fate (e.g. termination), coordinated replication, resource sharing, and dependency management are handled automatically for containers in a Pod.

In general, users shouldn’t need to create Pods directly. They should almost always use controllers even for singletons, for example, [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). Controllers provide self-healing with a cluster scope, as well as replication and rollout management. Controllers like [StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset.md) can also provide support to stateful Pods.

---

When a user requests deletion of a Pod, the system records the intended  grace period before the Pod is allowed to be forcefully killed, and a TERM signal is sent to the main process in each container. Once the  grace period has expired, the KILL signal is sent to those processes,  and the Pod is then deleted from the API server. If the Kubelet or the  container manager is restarted while waiting for processes to terminate, the termination will be retried with the full grace period.

Force deletion of a Pod is defined as deletion of a Pod from the cluster state and etcd immediately. When a force deletion is performed, the API server does not wait for confirmation from the kubelet that the Pod has been terminated on the node it was running on. It removes the Pod in  the API immediately so a new Pod can be created with the same name.

---

Any container in a Pod can enable privileged mode, using the `privileged` flag on the [security context](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) of the container spec. This is useful for containers that want to use  Linux capabilities like manipulating the network stack and accessing  devices.

##### Pod Lifecycle

Here are the possible values for `phase`:

| Value       | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| `Pending`   | The Pod has been accepted by the Kubernetes system, but one or more of the  Container images has not been created. This includes time before being  scheduled as well as time spent downloading images over the network,  which could take a while. |
| `Running`   | The Pod has been bound to a node, and all of the Containers have been  created. At least one Container is still running, or is in the process  of starting or restarting. |
| `Succeeded` | All Containers in the Pod have terminated in success, and will not be restarted. |
| `Failed`    | All Containers in the Pod have terminated, and at least one Container has  terminated in failure. That is, the Container either exited with  non-zero status or was terminated by the system. |
| `Unknown`   | For some reason the state of the Pod could not be obtained, typically due to an error in communicating with the host of the Pod. |

A [Probe](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#probe-v1-core) is a diagnostic performed periodically by the [kubelet](https://kubernetes.io/docs/admin/kubelet/) on a Container. To perform a diagnostic, the kubelet calls a [Handler](https://godoc.org/k8s.io/kubernetes/pkg/api/v1#Handler) implemented by the Container. There are three types of handlers:
- [ExecAction](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#execaction-v1-core): Executes a specified command inside the Container. The diagnostic is considered successful if the command exits with a status code of 0.
- [TCPSocketAction](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#tcpsocketaction-v1-core): Performs a TCP check against the Container’s IP address on a specified port. The diagnostic is considered successful if the port is open.
- [HTTPGetAction](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#httpgetaction-v1-core): Performs an HTTP Get request against the Container’s IP address on a specified port and path. The diagnostic is considered successful if the response has a status code greater than or equal to 200 and less than 400.

The kubelet can optionally perform and react to three kinds of probes on running Containers:
- `livenessProbe`: Indicates whether the Container is running. If the liveness probe fails, the kubelet kills the Container, and the Container is subjected to its [restart policy](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#restart-policy). If a Container does not provide a liveness probe, the default state is `Success`.
- `readinessProbe`: Indicates whether the Container is ready to service requests. If the readiness probe fails, the endpoints controller removes the Pod’s IP address from the endpoints of all Services that match the Pod. The default state of readiness before the initial delay is `Failure`. If a Container does not provide a readiness probe, the default state is `Success`.
- `startupProbe`: Indicates whether the application within the Container is started. All other probes are disabled if a startup probe is provided, until it succeeds. If the startup probe fails, the kubelet kills the Container, and the Container is subjected to its [restart policy](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#restart-policy). If a Container does not provide a startup probe, the default state is `Success`.

Once Pod is assigned to a node by scheduler, kubelet starts creating  containers using container runtime.There are three possible states of  containers: Waiting, Running and Terminated. 

A PodSpec has a `restartPolicy` field with possible values Always, OnFailure, and Never. The default value is Always. `restartPolicy` applies to all Containers in the Pod. `restartPolicy` only refers to restarts of the Containers by the kubelet on the same node. Exited Containers that are restarted by the kubelet are restarted with an exponential back-off delay (10s, 20s, 40s …) capped at five minutes, and is reset after ten minutes of successful execution. 

The control plane cleans up terminated Pods (with a phase of `Succeeded` or `Failed`), when the number of Pods exceeds the configured threshold (determined by `terminated-pod-gc-threshold` in the kube-controller-manager). This avoids a resource leak as Pods are created and terminated over time.

##### Init containers

A [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) can have multiple containers running apps within it, but it can also have one or more init containers, which are run before the app containers are started.

Init containers are exactly like regular containers, except:
- Init containers always run to completion.
- Each init container must complete successfully before the next one starts.

If a Pod’s init container fails, Kubernetes repeatedly restarts the Pod until the init container succeeds. However, if the Pod has a `restartPolicy` of Never, Kubernetes does not restart the Pod.

To specify an init container for a Pod, add the `initContainers` field into the Pod specification, as an array of objects of type [Container](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#container-v1-core), alongside the app `containers` array. The status of the init containers is returned in `.status.initContainerStatuses` field as an array of the container statuses (similar to the `.status.containerStatuses` field).

Because init containers have separate images from app containers, they have some advantages for start-up related code:
* Init containers can contain utilities or custom code for setup that are not present in an app image.
* The application image builder and deployer roles can work independently without the need to jointly build a single app image.
* Can run with a different view of the filesystem than app containers in the same Pod. Consequently, they can be given access to [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) that app containers cannot access.
* Because init containers run to completion before any app containers start, init containers offer a mechanism to block or delay app container startup until a set of preconditions are met. 
* Init containers can securely run utilities or custom code that would otherwise make an app container image less secure.

Example:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
```

##### Pod Preset

A `Pod Preset` is an API resource for injecting additional runtime requirements into a Pod at creation time. You use [label selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#label-selectors) to specify the Pods to which a given Pod Preset applies.

Using a Pod Preset allows pod template authors to not have to explicitly provide all information for every pod. This way, authors of pod templates consuming a specific service do not need to know all the details about that service.

Kubernetes provides an admission controller (`PodPreset`) which, when enabled, applies Pod Presets to incoming pod creation requests.

##### Pod Topology Spread Constraints

You can use *topology spread constraints* to control how [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) are spread across your cluster among failure-domains such as regions,  zones, nodes, and other user-defined topology domains. This can help to  achieve high availability as well as efficient resource utilization.

##### Disruptions

Pods do not disappear until someone (a person or a controller) destroys them, or there is an unavoidable hardware or system software error. We call these unavoidable cases involuntary disruptions to an application:
- a hardware failure of the physical machine backing the node
- cluster administrator deletes VM (instance) by mistake
- cloud provider or hypervisor failure makes VM disappear
- a kernel panic
- the node disappears from the cluster due to cluster network partition
- eviction of a pod due to the node being [out-of-resources](https://kubernetes.io/docs/tasks/administer-cluster/out-of-resource/).

We call other cases voluntary disruptions. These include both actions initiated by the application owner and those initiated by a Cluster Administrator. Typical application owner actions include:
- deleting the deployment or other controller that manages the pod
- updating a deployment’s pod template causing a restart
- directly deleting a pod (e.g. by accident)

Cluster Administrator actions include:
- [Draining a node](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) for repair or upgrade.
- Draining a node from a cluster to scale the cluster down (learn about [Cluster Autoscaling](https://kubernetes.io/docs/tasks/administer-cluster/cluster-management/#cluster-autoscaler) ).
- Removing a pod from a node to permit something else to fit on that node.

Here are some ways to mitigate involuntary disruptions:
- Ensure your pod [requests the resources](https://kubernetes.io/docs/tasks/configure-pod-container/assign-cpu-ram-container) it needs.
- Replicate your application if you need higher availability. ([stateless](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/) and [stateful](https://kubernetes.io/docs/tasks/run-application/run-replicated-stateful-application/) applications.)
- Spread applications across racks (using [anti-affinity](https://kubernetes.io/docs/user-guide/node-selection/#inter-pod-affinity-and-anti-affinity-beta-feature)) or across zones (if using a [multi-zone cluster](https://kubernetes.io/docs/setup/multiple-zones).)

##### Disruption Budgets

An Application Owner can create a `PodDisruptionBudget` object (PDB) for each application. A PDB limits the number of pods of a replicated application that are down simultaneously from voluntary disruptions. 

Cluster managers and hosting providers should use tools which respect Pod Disruption Budgets by calling the [Eviction API](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/#the-eviction-api) instead of directly deleting pods or deployments. Examples are the `kubectl drain` command and the Kubernetes-on-GCE cluster upgrade script (`cluster/gce/upgrade.sh`).

##### Ephemeral Containers

A special type of container that runs temporarily in an existing [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) to accomplish user-initiated actions such as troubleshooting. You use ephemeral containers to inspect services rather than to build applications.

Ephemeral containers are described using the same `ContainerSpec` as regular containers, but many fields are incompatible and disallowed for ephemeral containers:
- Ephemeral containers may not have ports, so fields such as `ports`, `livenessProbe`, `readinessProbe` are disallowed.
- Pod resource allocations are immutable, so setting `resources` is disallowed.
- For a complete list of allowed fields, see the [EphemeralContainer reference documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#ephemeralcontainer-v1-core).