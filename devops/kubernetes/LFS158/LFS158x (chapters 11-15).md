### Chapter 11. Deploying a standalone application

**Via Dashboard**

```bash
minikube start
```

From the dashboard, click on the *CREATE* tab at the top right corner of the Dashboard. Click on the *CREATE AN APP* tab and provide the following application details:

- The application name is **webserver**
- The Docker image to use is **nginx:alpine**, where **alpine** is the image tag
- The replica count, or the number of Pods, is 3
- No Service, as we will be creating it later.

Hit Deploy.

Study cluster:

```bash
kubectl get deployments
kubectl get replicasets
kubectl get pods
kubectl describe pod xxxxxxxxxxxx
kubectl get pods -L k8s-app,label2
kubectl get pods -l k8s-app=webserver
```

Delete:

```bash
kubectl delete deployments webserver

kubectl get replicasets
kubectl get pods
```


**Via CLI**

```bash
cat > webserver.yaml <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webserver
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80
EOF
```

```bash
kubectl create -f webserver.yaml

kubectl get replicasets
kubectl get pods
```


**Exposing an Application**

```bash
cat > webserver-svc.yaml <<EOF
apiVersion: v1
kind: Service
metadata:
  name: web-service
  labels:
    run: web-service
spec:
  type: NodePort
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: nginx 
EOF
```

```bash
kubectl create -f webserver-svc.yaml
```


Or expose existing deployment:
```bash
kubectl expose deployment webserver --name=web-service --type=NodePort
```

Check:
```bash
kubectl get services
kubectl describe service web-service

minikube service web-service

# or get ip with
minikube ip
```


**Liveness and Readiness Probes**

While containerized applications are scheduled to run in pods on nodes across our cluster, at times the applications may become unresponsive or may be delayed during startup. Implementing **Liveness** and **Readiness Probes** allows the **kubelet** to control the health of the application running inside a Pod's container and force a container restart of an unresponsive application. 

When defining both **Readiness** and **Liveness Probes**, it is recommended to allow enough time for the **Readiness Probe** to possibly fail a few times before a pass, and only then check the **Liveness Probe**. If **Readiness** and **Liveness Probes** overlap there may be a risk that the container never reaches ready state. 

Liveness Probes can be set by defining:

- Liveness command

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5
```

- Liveness HTTP request

```yaml
livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
        httpHeaders:
        - name: X-Custom-Header
          value: Awesome
      initialDelaySeconds: 3
      periodSeconds: 3
```

- TCP Liveness Probe.

```bash
livenessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 20
```


Readiness Probes example:
```yaml
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```


### Chapter 12. Kubernetes Volume Management

**Volumes**

A Volume is essentially a directory backed by a storage medium. The storage medium, content and access mode are determined by the Volume Type.

 <img src=".LFS158x (chapters 11-x)-images/podvolume.png" alt="Volume" style="zoom:50%;" />

In Kubernetes, a Volume is attached to a Pod and can be shared among the containers of that Pod. The Volume has the same life span as the Pod, and it outlives the containers of the Pod - this allows data to be preserved across container restarts.

A Volume Type decides the properties of the directory, like size, content, default access modes, etc.:

- **emptyDir**
  An **empty** Volume is created for the Pod as soon as it is scheduled on the worker node. The Volume's life is tightly coupled with the Pod. If the Pod is terminated, the content of **emptyDir** is deleted forever.  
- **hostPath**
  With the **hostPath** Volume Type, we can share a directory from the host to the Pod. If the Pod is terminated, the content of the Volume is still available on the host.
- **gcePersistentDisk**
  With the **gcePersistentDisk** Volume Type, we can mount a [Google Compute Engine (GCE) persistent disk](https://cloud.google.com/compute/docs/disks/) into a Pod.
- **awsElasticBlockStore**
  With the **awsElasticBlockStore** Volume Type, we can mount an [AWS EBS Volume](https://aws.amazon.com/ebs/) into a Pod. 
- **azureDisk**
  With **azureDisk** we can mount a [Microsoft Azure Data Disk](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/managed-disks-overview) into a Pod.
- **azureFile** 
  With **azureFile** we can mount a [Microsoft Azure File Volume](https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md) into a Pod.
- **cephfs** 
  With **cephfs**, an existing CephFS volume can be mounted into a Pod. When a Pod terminates, the volume is unmounted and the contents of the volume are preserved.
- **nfs**
  With [nfs](https://en.wikipedia.org/wiki/Network_File_System), we can mount an NFS share into a Pod.
- **iscsi**
  With [iscsi](https://en.wikipedia.org/wiki/ISCSI), we can mount an iSCSI share into a Pod.
- **secret**
  With the **secret** Volume Type, we can pass sensitive information, such as passwords, to Pods. We will take a look at an example in a later chapter.
- **configMap** 
  With **configMap** objects, we can provide configuration data, or shell commands and arguments into a Pod.
- **persistentVolumeClaim**
  We can attach a [PersistentVolume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) to a Pod using a **persistentVolumeClaim**. We will cover this in our next section. 


**PersistentVolumes**

Provides APIs for users and administrators to manage and consume persistent storage. To manage the Volume, it uses the PersistentVolume API resource type, and to consume it, it uses the PersistentVolumeClaim API resource type.

A Persistent Volume is a network-attached storage in the cluster, which is provisioned by the administrator.

<img src=".LFS158x (chapters 11-x)-images/pv.png" alt="Persistent Volume" style="zoom:50%;" />

PersistentVolumes can be dynamically provisioned based on the StorageClass resource. A StorageClass contains pre-defined provisioners and parameters to create a PersistentVolume. Using PersistentVolumeClaims, a user sends the request for dynamic PV creation, which gets wired to the StorageClass resource.


**PersistentVolumeClaims**

A **PersistentVolumeClaim (PVC)** is a request for storage by a user. Users request for PersistentVolume resources based on type, access mode, and size. 

There are three access modes: 
- ReadWriteOnce (read-write by a single node)
- ReadOnlyMany (read-only by many nodes)
- ReadWriteMany (read-write by many nodes).

Once a suitable PersistentVolume is found, it is bound to a PersistentVolumeClaim.  

<img src=".LFS158x (chapters 11-x)-images/pvc1.png" alt="Persistent Volume Claim" style="zoom:50%;" />

After a successful bound, the PersistentVolumeClaim resource can be used in a Pod. 

![Persistent Volume Claim used in a Pod](.LFS158x (chapters 11-x)-images/pvc2.png)

Once a user finishes its work, the attached PersistentVolumes can be released. The underlying PersistentVolumes can then be reclaimed (for an admin to verify and/or aggregate data), deleted (both data and volume are deleted), or recycled for future usage (only data is deleted). 


### Chapter 13. ConfigMaps and Secrets

While deploying an application, we may need to pass such runtime parameters like configuration details, permissions, passwords, tokens, etc. 

We can use the **ConfigMap API** resource to pass custom data as runtime parameters via template image.

We can use the **Secret API** resource to pass sensitive information.


**ConfigMaps**

[ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) allow us to decouple the configuration details from the container image. Using ConfigMaps, we pass configuration data as key-value pairs, which are consumed by Pods or any other system components and controllers, in the form of environment variables, sets of commands and arguments, or volumes. We can create ConfigMaps from literal values, from configuration files, from one or more files or directories.

```bash
# create ConfigMap from literal values
kubectl create configmap my-config --from-literal=key1=value1 --from-literal=key2=value2

kubectl get configmaps my-config -o yaml
```

```yaml
# create ConfigMap from configuration file 
apiVersion: v1
kind: ConfigMap
metadata:
  name: customer1
data:
  TEXT1: Customer1_Company
  TEXT2: Welcomes You
  COMPANY: Customer1 Company Technology Pct. Ltd.
```

```bash
kubectl create -f xxxxxxx.yaml
```

```bash
# create ConfigMap from file permission-reset.properties
permission=read-only
allowed="true"
resetCount=3
```
```bash
kubectl create configmap permission-config --from-file=permission-reset.properties
```


**Use ConfigMaps Inside Pods**

As Environment Variables:

```yaml
 containers:
  - name: myapp-full-container
    image: myapp
    envFrom:
    - configMapRef:
      name: full-config-map
```

```yaml
  containers:
  - name: myapp-specific-container
    image: myapp
    env:
    - name: SPECIFIC_ENV_VAR1
      valueFrom:
        configMapKeyRef:
          name: config-map-1
          key: SPECIFIC_DATA
    - name: SPECIFIC_ENV_VAR2
      valueFrom:
        configMapKeyRef:
          name: config-map-2
          key: SPECIFIC_INFO
```

As Volumes:

```yaml
 containers:
  - name: myapp-vol-container
    image: myapp
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: vol-config-map
```


**Secrets**

[Secret](https://kubernetes.io/docs/concepts/configuration/secret/) object can help by allowing us to encode the sensitive information before sharing it. With Secrets, we can share sensitive information like passwords, tokens, or keys in the form of key-value pairs, similar to ConfigMaps; thus, we can control how the information in a Secret is used, reducing the risk for accidental exposures. In Deployments or other resources, the Secret object is *referenced*, without exposing its content.

Secret data is stored as plain text inside **etcd**. Data to be encrypted at rest while it is stored in **etcd**; a feature which needs to be enabled at the API server level.

```bash
# create Secrets from literal values
kubectl create secret generic my-password --from-literal=password=mysqlpassword

kubectl get secret my-password
kubectl describe secret my-password
```

```bash
# create a Secret manually

# each value of a sensitive information field must be encoded using base64
echo mysqlpassword | base64

# configuration file
apiVersion: v1
kind: Secret
metadata:
  name: my-password
type: Opaque
data:
  password: bXlzcWxwYXNzd29yZAo=

kubectl create -f mypass.yaml
```

```bash
# create Secret from file
echo mysqlpassword | base64
echo -n 'xxxxxxxxxxxx' > password.txt
kubectl create secret generic my-file-password --from-file=password.txt

kubectl get secret my-file-password
kubectl describe secret my-file-password
```


**Use Secrets Inside Pods**

As Environment Variables:

```yaml
spec:
  containers:
  - image: wordpress:4.7.3-apache
    name: wordpress
    env:
    - name: WORDPRESS_DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-password
          key: password
```

As Files from a Pod:

```yaml
spec:
  containers:
  - image: wordpress:4.7.3-apache
    name: wordpress
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/secret-data"
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: my-password
```


### Chapter 14. Ingress

Represents another layer of abstraction, deployed in front of the *Service* API resources, offering a unified method of managing access to our applications from the external world.

An Ingress is a collection of rules that allow inbound connections to reach the cluster Services.

To allow the inbound connection to reach the cluster Services, Ingress configures a Layer 7 HTTP/HTTPS load balancer for Services and provides the following:

- TLS (Transport Layer Security)
  - Name-based virtual hosting 
  - Fanout routing
  - Loadbalancing
  - Custom rules.

<img src=".LFS158x (chapters 11-x)-images/ingress_updated.png" alt="Ingress" style="zoom:50%;" />

Example:

* Name-Based Virtual Hosting 

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: virtual-host-ingress
  namespace: default
spec:
  rules:
  - host: blue.example.com
    http:
      paths:
      - backend:
          serviceName: webserver-blue-svc
          servicePort: 80
  - host: green.example.com
    http:
      paths:
      - backend:
          serviceName: webserver-green-svc
          servicePort: 80
```

* Fanout

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: fan-out-ingress
  namespace: default
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /blue
        backend:
          serviceName: webserver-blue-svc
          servicePort: 80
      - path: /green
        backend:
          serviceName: webserver-green-svc
          servicePort: 80
```


**Ingress Controller**

Is an application watching the Master Node's API server for changes in the Ingress resources and updates the Layer 7 Load Balancer accordingly. Kubernetes supports different Ingress Controllers, and, if needed, we can also build our own. [GCE L7 Load Balancer Controller](https://github.com/kubernetes/ingress-gce/blob/master/README.md) and [Nginx Ingress Controller](https://github.com/kubernetes/ingress-nginx/blob/master/README.md) are commonly used Ingress Controllers. Other controllers are [Istio](https://istio.io/), [Kong](https://konghq.com/), [Traefik](https://github.com/containous/traefik), etc.

For minikube use:

```bash
minikube addons enable ingress
```

To deploy:

```bash
kubectl create -f virtual-host-ingress.yaml
```


### Chapter 15. Advanced Topics

**Annotations**

With [Annotations](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/), we can attach arbitrary non-identifying metadata to any objects, in a key-value format. Annotations can be used to:

* Store build/release IDs, PR numbers, git branch, etc.
* Phone/pager numbers of people responsible, or directory entries specifying where such information can be found.
* Pointers to logging, monitoring, analytics, audit repositories, debugging tools, etc.
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: webserver
  annotations:
    description: Deployment based PoC dates 2nd May'2019
```


**Jobs and CronJobs**

A [Job](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/) creates one or more Pods to perform a given task. The Job object takes the responsibility of Pod failures. It makes sure that the given task is completed successfully. Once the task is complete, all the Pods have terminated automatically. Job configuration options include **parallelism** - to set the number of pods allowed to run in parallel; **completions** - to set the number of expected completions; **activeDeadlineSeconds** - to set the duration of the Job; **backoffLimit** - to set the number of retries before Job is marked as failed; **ttlSecondsAfterFinished** - to delay the clean up of the finished Jobs.

[CronJobs](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/), where a new Job object is created about once per each execution cycle. The CronJob configuration options include **startingDeadlineSeconds** - to set the deadline to start a Job if scheduled time was missed; **concurrencyPolicy** - to *allow* or *forbid* concurrent Jobs or to *replace* old Jobs with new ones. 


**Quota Management**

Provides constraints that limit aggregate resource consumption per Namespace. Types:

- **Compute Resource Quota** - we can limit the total sum of compute resources (CPU, memory, etc.) that can be requested in a given Namespace.
- **Storage Resource Quota** - we can limit the total sum of storage resources (PersistentVolumeClaims, requests.storage, etc.) that can be requested.
- **Object Count Quota** - we can restrict the number of objects of a given type (pods, ConfigMaps, PersistentVolumeClaims, ReplicationControllers, Services, Secrets, etc.).


**Autoscaling**

Autoscaling can be implemented in a Kubernetes cluster via controllers which periodically adjust the number of running objects based on single, multiple, or custom metrics.

- [Horizontal Pod Autoscaler (HPA)](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#algorithm-details) 
  HPA is an algorithm based controller [API resource](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/autoscaling/horizontal-pod-autoscaler.md#horizontalpodautoscaler-object) which automatically adjusts the number of replicas in a ReplicaSet, Deployment or Replication Controller based on CPU utilization.

- [Vertical Pod Autoscaler (VPA)](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/autoscaling/vertical-pod-autoscaler.md) 
  VPA automatically sets Container resource requirements (CPU and memory) in a Pod and dynamically adjusts them in runtime, based on historical utilization data, current resource availability and real-time events.

- [Cluster Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler) 
  Cluster Autoscaler automatically re-sizes the Kubernetes cluster when there are insufficient resources available for new Pods expecting to be scheduled or when there are underutilized nodes in the cluster.


**DaemonSets**

Collects monitoring data from all nodes, or runs a storage daemon on all nodes.

The **kube-proxy** agent running as a Pod on every single node in the cluster is managed by a **DaemonSet**.

Whenever a node is added to the cluster, a Pod from a given DaemonSet is automatically created on it. DaemonSet's Pods are placed on nodes by the cluster's default Scheduler. When the node dies or it is removed from the cluster, the respective Pods are garbage collected. If a DaemonSet is deleted, all Pods it created are deleted as well.

Can be scheduled only on specific nodes by configuring **nodeSelectors** and node **affinity** rules. DaemonSets support rolling updates and rollbacks. 


**StatefulSets**

The [StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) controller is used for stateful applications which require a unique identity, such as name, network identifications, strict ordering, etc.

Provides identity and guaranteed ordering of deployment and scaling to Pods. Supports rolling updates and rollbacks. 


**Kubernetes Federation**

With [Kubernetes Cluster Federation](https://kubernetes.io/docs/concepts/cluster-administration/federation/) we can manage multiple Kubernetes clusters from a single control plane. We can sync resources across the federated clusters and have cross-cluster discovery. This allows us to perform Deployments across regions, access them using a global DNS record, and achieve High Availability. 

Useful when we want to build a hybrid solution, in which we can have one cluster running inside our private datacenter and another one in the public cloud, allowing us to avoid provider lock-in.


**Custom Resources**

In Kubernetes, a **resource** is an API endpoint which stores a collection of API objects. For example, a Pod resource contains all the Pod objects.

We can also create new resources using **custom resources**. With custom resources, we don't have to modify the Kubernetes source.

Custom resources are dynamic in nature, and they can appear and disappear in an already running cluster at any time.

To make a resource declarative, we must create and install a **custom controller**, which can interpret the resource structure and perform the required actions. Custom controllers can be deployed and managed in an already running cluster.

There are two ways to add custom resources:

- [Custom Resource Definitions (CRDs)](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)
- [API Aggregation](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/apiserver-aggregation/)
For more fine-grained control, we can write API Aggregators. They are subordinate API servers which sit behind the primary API server. The primary API server acts as a proxy for all incoming API requests - it handles the ones based on its capabilities and proxies over the other requests meant for the subordinate API servers.


**Helm**

To deploy an application, we use different Kubernetes manifests, such as Deployments, Services, Volume Claims, Ingress, etc. Sometimes, it can be tiresome to deploy them one by one. We can bundle all those manifests after templatizing them into a well-defined format, along with other metadata. Such a bundle is referred to as *Chart*.

[Helm](https://helm.sh/) is a package manager (analogous to **yum** and **apt** for Linux) for Kubernetes, which can install/update/delete those Charts in the Kubernetes cluster.

Helm has two components:
- A client called *helm*, which runs on your user's workstation
- A server called *tiller*, which runs inside your Kubernetes cluster.

The client *helm* connects to the server *tiller* to manage Charts. Charts submitted for Kubernetes are available [here](https://github.com/helm/charts).


**Security Contexts and Pod Security Policies**

At times we need to define specific privileges and access control settings for Pods and Containers. **Security Contexts** allow us to set Discretionary Access Control for object access permissions, privileged running, capabilities, security labels, etc. However, their effect is limited to the individual Pods and Containers where such context configuration settings are incorporated in the **spec** section.

In order to apply security settings to multiple Pods and Containers cluster-wide, we can define **Pod Security Policies**. They allow more fine-grained security settings to control the usage of the host namespace, host networking and ports, file system groups, usage of volume types, enforce Container user and group ID, root privilege escalation, etc.


**Network Policies**

Sets of rules which define how Pods are allowed to talk to other Pods and resources inside and outside the cluster. Pods not covered by any **Network Policy** will continue to receive unrestricted traffic from any endpoint. 

Very similar to typical Firewalls. They are designed to protect mostly assets located inside the Firewall but can restrict outgoing traffic as well based on sets of rules and policies.

The Network Policy API resource specifies **podSelectors**, **Ingress** and/or **Egress** policyTypes, and rules based on source and destination **ipBlocks** and **ports**. 


**Monitoring and Logging**

- **Metrics Server** 
  [Metrics Server](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server) is a cluster-wide aggregator of resource usage data.
- **Prometheus** can also be used to scrape the resource usage from different Kubernetes components and objects. Using its client libraries, we can also instrument the code of our application.

The most common way to collect the logs is using [Elasticsearch](https://kubernetes.io/docs/tasks/debug-application-cluster/logging-elasticsearch-kibana/), which uses [fluentd](http://www.fluentd.org/) with custom configuration as an agent on the nodes. **fluentd** is an open source data collector.