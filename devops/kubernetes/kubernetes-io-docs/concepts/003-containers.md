## Images

The default pull policy is `IfNotPresent` which causes the Kubelet to skip pulling an image if it already exists.

---

Docker CLI now supports the following command `docker manifest` with sub commands like `create`, `annotate` and `push`. These commands can be used to build and push the manifests. You can use `docker manifest inspect` to view the manifest.

---

Private registries may require keys to read images from them. Credentials can be provided in several ways:
* Using Google Container Registry
  Kubernetes has native support for the [Google Container Registry (GCR)](https://cloud.google.com/tools/container-registry/), when running on Google Compute Engine (GCE). If you are running your cluster on GCE or Google Kubernetes Engine, simply use the full image name (e.g. `gcr.io/my_project/image:tag`). All pods in a cluster will have read access to images in this registry. The kubelet will authenticate to GCR using the instance’s Google service account. 
  
* Using Amazon Elastic Container Registry

* Using Oracle Cloud Infrastructure Registry (OCIR)

* Using Azure Container Registry (ACR)

* Using IBM Cloud Container Registry

* Configuring Nodes to Authenticate to a Private Registry
  Docker stores keys for private registries in the `$HOME/.dockercfg` or `$HOME/.docker/config.json` file. 
  
* Pre-pulled Images
  By default, the kubelet will try to pull each image from the specified registry. However, if the `imagePullPolicy` property of the container is set to `IfNotPresent` or `Never`, then a local image is used (preferentially or exclusively, respectively).
  
* Specifying ImagePullSecrets on a Pod
```bash
# Creating a Secret with a Docker Config
kubectl create secret docker-registry <name> --docker-server=DOCKER_REGISTRY_SERVER --docker-username=DOCKER_USER --docker-password=DOCKER_PASSWORD --docker-email=DOCKER_EMAIL
  
# Referring to an imagePullSecrets on a Pod
cat <<EOF > pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: foo
  namespace: awesomeapps
spec:
  containers:
    - name: foo
      image: janedoe/awesomeapp:v1
  imagePullSecrets:
    - name: myregistrykey
EOF
  
cat <<EOF >> ./kustomization.yaml
resources:
- pod.yaml
EOF
```


## Container Environment Variables

The Kubernetes Container environment provides several important resources to Containers:
- A filesystem, which is a combination of an [image](https://kubernetes.io/docs/concepts/containers/images/) and one or more [volumes](https://kubernetes.io/docs/concepts/storage/volumes/).
- Information about the Container itself:
  - The hostname of a Container is the name of the Pod in which the Container is running.
  - The Pod name and namespace are available as environment variables through the [downward API](https://kubernetes.io/docs/tasks/inject-data-application/downward-api-volume-expose-pod-information/).
  - User defined environment variables from the Pod definition.
  - any environment variables specified statically in the Docker image.
- Information about other objects in the cluster:
  - A list of all services that were running when a Container was created


## Runtime Class

RuntimeClass is a feature for selecting the container runtime configuration. The container runtime configuration is used to run a Pod’s containers.

The configurations available through RuntimeClass are Container Runtime Interface (CRI) implementation dependent:
* dockershim: Kubernetes built-in dockershim CRI does not support runtime handlers.
* [containerd](https://containerd.io/): Runtime handlers are configured through containerd’s configuration at `/etc/containerd/config.toml`.
* [cri-o](https://cri-o.io/): Runtime handlers are configured through cri-o’s configuration at `/etc/crio/crio.conf`. 

Once RuntimeClasses are configured for the cluster, using them is very simple:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  runtimeClassName: myclass
  # ...
```

RuntimeClass includes support for heterogenous clusters through its `scheduling` fields. Through the use of these fields, you can ensure that pods running with this RuntimeClass are scheduled to nodes that support it.

RuntimeClass includes support for specifying overhead associated with running a pod, as part of the [`PodOverhead`](https://kubernetes.io/docs/concepts/configuration/pod-overhead/) feature.


## Container Lifecycle Hooks

The hooks enable Containers to be aware of events in their management lifecycle and run code implemented in a handler when the corresponding lifecycle hook is executed:
* PostStart - This hook executes immediately after a container is created. However, there is no guarantee that the hook will execute before the container ENTRYPOINT. No parameters are passed to the handler.
* PreStop - This hook is called immediately before a container is terminated due to an API request or management event such as liveness probe failure, preemption, resource contention and others. It is blocking.

There are two types of hook handlers that can be implemented for Containers:
- Exec - Executes a specific command, such as `pre-stop.sh`, inside the cgroups and namespaces of the Container. Resources consumed by the command are counted against the Container.
- HTTP - Executes an HTTP request against a specific endpoint on the Container.

Hook handler calls are synchronous within the context of the Pod containing the Container. If the hook takes too long to run or hangs, the Container cannot reach a right state. Users should make their hook handlers as lightweight as possible.

Hook delivery is intended to be at least once, which means that a hook may be called multiple times for any given event.

The logs for a Hook handler are not exposed in Pod events. If a handler fails for some reason, it broadcasts an event. For `PostStart`, this is the `FailedPostStartHook` event, and for `PreStop`, this is the `FailedPreStopHook` event. You can see these events by running `kubectl describe pod`.

