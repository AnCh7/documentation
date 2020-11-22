# Limit Ranges

By default, containers run with unbounded [compute resources](https://kubernetes.io/docs/user-guide/compute-resources) on a Kubernetes cluster. With Resource quotas, cluster administrators can restrict the resource  consumption and creation on a namespace basis. Within a namespace, a Pod or Container can consume as much CPU and  memory as defined by the namespace’s resource quota. There is a concern  that one Pod or Container could monopolize all of the resources. Limit  Range is a policy to constrain resource by Pod or Container in a  namespace.

A limit range, defined by a `LimitRange` object, provides constraints that can:
- Enforce minimum and maximum compute resources usage per Pod or Container in a namespace.
- Enforce minimum and maximum storage request per PersistentVolumeClaim in a namespace.
- Enforce a ratio between request and limit for a resource in a namespace.
- Set default request/limit for compute resources in a namespace and automatically inject them to Containers at runtime.

#### Enabling Limit Range

Limit Range support is enabled by default for many Kubernetes distributions. It is enabled when the apiserver `--enable-admission-plugins=` flag has `LimitRanger` admission controller as one of its arguments. 

A limit range is enforced in a particular namespace when there is a `LimitRange` object in that namespace.

If creating or updating a resource (Pod, Container,  PersistentVolumeClaim) violates a limit range constraint, the request to the API server will fail with HTTP status code `403 FORBIDDEN` and a message explaining the constraint that would have been violated.

LimitRange validations occurs only at Pod Admission stage, not on Running pods.

**Limiting Container compute resources**

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: limit-mem-cpu-per-container
spec:
  limits:
  - max:
      cpu: "800m"
      memory: "1Gi"
    min:
      cpu: "100m"
      memory: "99Mi"
    default:
      cpu: "700m"
      memory: "900Mi"
    defaultRequest:
      cpu: "110m"
      memory: "111Mi"
    type: Container
```

**Limiting Pod compute resources**

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: limit-mem-cpu-per-pod
spec:
  limits:
  - max:
      cpu: "2"
      memory: "2Gi"
    type: Pod
```

**Limiting Storage resources**

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: storagelimits
spec:
  limits:
  - type: PersistentVolumeClaim
    max:
      storage: 2Gi
    min:
      storage: 1Gi
```

**Limits/Requests Ratio**

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: limit-memory-ratio-pod
spec:
  limits:
  - maxLimitRequestRatio:
      memory: 2
    type: Pod
```


# Resource Quotas

When several users or teams share a cluster with a fixed number of nodes, there is a concern that one team could use more than its fair share of resources.

Resource quotas are a tool for administrators to address this concern.

A resource quota, defined by a `ResourceQuota` object, provides constraints that limit aggregate resource consumption per namespace. It can limit the quantity of objects that can be created in a namespace by type, as well as the total amount of compute resources that may be consumed by resources in that project.

Resource Quota support is enabled by default for many Kubernetes distributions. It is enabled when the apiserver `--enable-admission-plugins=` flag has `ResourceQuota` as one of its arguments.

**Compute Resource Quota**

| Resource Name     | Description                                                  |
| ----------------- | ------------------------------------------------------------ |
| `limits.cpu`      | Across all pods in a non-terminal state, the sum of CPU limits cannot exceed this value. |
| `limits.memory`   | Across all pods in a non-terminal state, the sum of memory limits cannot exceed this value. |
| `requests.cpu`    | Across all pods in a non-terminal state, the sum of CPU requests cannot exceed this value. |
| `requests.memory` | Across all pods in a non-terminal state, the sum of memory requests cannot exceed this value. |

In addition to the resources mentioned above, in release 1.10, quota support for [extended resources](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#extended-resources) is added.

**Storage Resource Quota**

You can limit the total sum of [storage resources](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) that can be requested in a given namespace. In addition, you can limit consumption of storage resources based on associated storage-class.

| Resource Name                                         | Description                                                  |
| ----------------------------------------------------- | ------------------------------------------------------------ |
| `requests.storage`                                    | Across all persistent volume claims, the sum of storage requests cannot exceed this value. |
| `persistentvolumeclaims`                              | The total number of [persistent volume claims](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims) that can exist in the namespace. |
| `.storageclass.storage.k8s.io/requests.storage`       | Across all persistent volume claims associated with the storage-class-name,  the sum of storage requests cannot exceed this value. |
| `.storageclass.storage.k8s.io/persistentvolumeclaims` | Across all persistent volume claims associated with the storage-class-name, the total number of [persistent volume claims](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims) that can exist in the namespace. |

**Object Count Quota**

The 1.9 release added support to quota all standard namespaced resource types using the following syntax:
- `count/<resource>.<group>`

When using `count/*` resource quota, an object is charged against the quota if it exists in server storage. These types of quotas are useful to protect against exhaustion of storage resources. For example, you may want to quota the number of secrets in a server given their large size.

| Resource Name            | Description                                                  |
| ------------------------ | ------------------------------------------------------------ |
| `configmaps`             | The total number of config maps that can exist in the namespace. |
| `persistentvolumeclaims` | The total number of [persistent volume claims](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims) that can exist in the namespace. |
| `pods`                   | The total number of pods in a non-terminal state that can exist in the namespace. A pod is in a terminal state if `.status.phase in (Failed, Succeeded)` is true. |
| `replicationcontrollers` | The total number of replication controllers that can exist in the namespace. |
| `resourcequotas`         | The total number of [resource quotas](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#resourcequota) that can exist in the namespace. |
| `services`               | The total number of services that can exist in the namespace. |
| `services.loadbalancers` | The total number of services of type load balancer that can exist in the namespace. |
| `services.nodeports`     | The total number of services of type node port that can exist in the namespace. |
| `secrets`                | The total number of secrets that can exist in the namespace. |

**Quota Scopes**

Each quota can have an associated set of scopes. A quota will only measure usage for a resource if it matches the intersection of enumerated scopes.

When a scope is added to the quota, it limits the number of resources it supports to those that pertain to the scope. Resources specified on the quota outside of the allowed set results in a validation error.

| Scope            | Description                                                 |
| ---------------- | ----------------------------------------------------------- |
| `Terminating`    | Match pods where `.spec.activeDeadlineSeconds >= 0`         |
| `NotTerminating` | Match pods where `.spec.activeDeadlineSeconds is nil`       |
| `BestEffort`     | Match pods that have best effort quality of service.        |
| `NotBestEffort`  | Match pods that do not have best effort quality of service. |

Pods can be created at a specific [priority](https://kubernetes.io/docs/concepts/configuration/pod-priority-preemption/#pod-priority). You can control a pod’s consumption of system resources based on a pod’s priority, by using the `scopeSelector` field in the quota spec.

A quota is matched and consumed only if `scopeSelector` in the quota spec selects the pod.

**Requests vs Limits**

When allocating compute resources, each container may specify a request and a limit value for either CPU or memory. The quota can be configured to quota either value.

If the quota has a value specified for `requests.cpu` or `requests.memory`, then it requires that every incoming container makes an explicit request for those resources. If the quota has a value specified for `limits.cpu` or `limits.memory`, then it requires that every incoming container specifies an explicit limit for those resources.

**Quota and Cluster Capacity**

`ResourceQuotas` are independent of the cluster capacity. They are expressed in absolute units. So, if you add nodes to your cluster, this does *not* automatically give each namespace the ability to consume more resources.

**Limit Priority Class consumption by default**

It may be desired that pods at a particular priority, eg.  “cluster-services”, should be allowed in a namespace, if and only if, a  matching quota object exists.

With this mechanism, operators will  be able to restrict usage of certain high priority classes to a limited  number of namespaces and not every namespace will be able to consume  these priority classes by default. To enforce this, kube-apiserver flag `--admission-control-config-file` should be used to pass path to the [following](https://kubernetes.io/docs/concepts/policy/resource-quotas/#limit-priority-class-consumption-by-default) configuration file.


# Pod Security Policies

Pod Security Policies enable fine-grained authorization of pod creation and updates.

A Pod Security Policy is a cluster-level resource that controls security sensitive aspects of the pod specification. The [PodSecurityPolicy](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#podsecuritypolicy-v1beta1-policy) objects define a set of conditions that a pod must run with in order to be accepted into the system, as well as defaults for the related fields. They allow an administrator to control the following:

| Control Aspect                                    | Field Names                                                  |
| ------------------------------------------------- | ------------------------------------------------------------ |
| Running of privileged containers                  | [`privileged`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#privileged) |
| Usage of host namespaces                          | [`hostPID`, `hostIPC`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#host-namespaces) |
| Usage of host networking and ports                | [`hostNetwork`, `hostPorts`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#host-namespaces) |
| Usage of volume types                             | [`volumes`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#volumes-and-file-systems) |
| Usage of the host filesystem                      | [`allowedHostPaths`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#volumes-and-file-systems) |
| White list of FlexVolume drivers                  | [`allowedFlexVolumes`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#flexvolume-drivers) |
| Allocating an FSGroup that owns the pod’s volumes | [`fsGroup`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#volumes-and-file-systems) |
| Requiring the use of a read only root file system | [`readOnlyRootFilesystem`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#volumes-and-file-systems) |
| The user and group IDs of the container           | [`runAsUser`, `runAsGroup`, `supplementalGroups`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#users-and-groups) |
| Restricting escalation to root privileges         | [`allowPrivilegeEscalation`, `defaultAllowPrivilegeEscalation`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#privilege-escalation) |
| Linux capabilities                                | [`defaultAddCapabilities`, `requiredDropCapabilities`, `allowedCapabilities`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#capabilities) |
| The SELinux context of the container              | [`seLinux`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#selinux) |
| The Allowed Proc Mount types for the container    | [`allowedProcMountTypes`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#allowedprocmounttypes) |
| The AppArmor profile used by containers           | [annotations](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#apparmor) |
| The seccomp profile used by containers            | [annotations](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp) |
| The sysctl profile used by containers             | [`forbiddenSysctls`,`allowedUnsafeSysctls`](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#sysctl) |

**Enabling Pod Security Policies**

Pod security policy control is implemented as an optional (but recommended) [admission controller](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#podsecuritypolicy). PodSecurityPolicies are enforced by [enabling the admission controller](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#how-do-i-turn-on-an-admission-control-plug-in), but doing so without authorizing any policies **will prevent any pods from being created** in the cluster.

**Authorizing Policies**

When a PodSecurityPolicy resource is created, it does nothing. In order to use it, the requesting user or target pod’s [service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) must be authorized to use the policy, by allowing the `use` verb on the policy.

[RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) is a standard Kubernetes authorization mode, and can easily be used to authorize use of policies.

**Policy Order**

In addition to restricting pod creation and update, pod security policies can also be used to provide default values for many of the fields that it controls.

**Policy Reference**

https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference
