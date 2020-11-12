# Cluster Management

#### Upgrading a cluster

The current state of cluster upgrades is provider dependent, and some  releases may require special care when upgrading. It is recommended that administrators consult both the [release notes](https://git.k8s.io/kubernetes/CHANGELOG.md), as well as the version specific upgrade notes prior to upgrading their clusters. Different providers, and tools, will manage upgrades differently. It is  recommended that you consult their main documentation regarding  upgrades.

`Google Compute Engine Open Source (GCE-OSS)` support master upgrades by deleting and recreating the master, while maintaining the same Persistent Disk (PD) to ensure that data is retained across the upgrade. Node upgrades for GCE use a [Managed Instance Group](https://cloud.google.com/compute/docs/instance-groups/), each node is sequentially destroyed and then recreated with new software. Any Pods that are running on that node need to be controlled by a Replication Controller, or manually re-created after the roll out.

`Google Kubernetes Engine` automatically updates master components (e.g. `kube-apiserver`, `kube-scheduler`) to the latest version. It also handles upgrading the operating system and other components that the master runs on.

#### Resizing a cluster

If your cluster runs short on resources you can easily add more machines to it if your cluster is running in [Node self-registration mode](https://kubernetes.io/docs/admin/node/#self-registration-of-nodes). If you’re using `GCE or Google Kubernetes Engine` it’s done by resizing the Instance Group managing your Nodes.

If you are using `GCE or Google Kubernetes Engine`, you can configure your cluster so that it is **automatically rescaled** based on pod needs.

#### Maintenance on a Node

If you need to reboot a node (such as for a kernel upgrade, libc upgrade, hardware repair, etc.), and the downtime is brief, then when the Kubelet restarts, it will attempt to restart the pods scheduled to it.

If the reboot takes longer (the default time is 5 minutes, controlled by `--pod-eviction-timeout` on the controller-manager), then the node controller will terminate the pods that are bound to the unavailable node. If there is a corresponding replica set (or replication controller), then a new copy of the pod will be started on a different node.

If you want more control over the upgrading process, you may use the following workflow:
- Use `kubectl drain` to gracefully terminate all pods on the node while marking the node as unschedulable.
- Make the node schedulable again `kubectl uncordon $NODENAME`.

#### Upgrading to a different API version

When a new API version is released, you may need to upgrade a cluster to support the new API version (e.g. switching from ‘v1’ to ‘v2’ when ‘v2’ is launched).

1. Turn on the new API version.
1. Upgrade the cluster’s storage to use the new version.
1. Upgrade all config files. Identify users of the old API version endpoints.
1. Update existing objects in the storage to new version by running `cluster/update-storage-objects.sh`.
1. Turn off the old API version

#### Turn on or off an API version for your cluster

Specific API versions can be turned on or off by passing `--runtime-config=api/` flag while bringing up the API server.

#### Switching your cluster’s storage API version

The objects that are stored to disk for a cluster’s internal  representation of the Kubernetes resources active in the cluster are  written using a particular version of the API. When the supported API changes, these objects may need to be rewritten  in the newer API. Failure to do this will eventually result in resources that are no longer decodable or usable by the Kubernetes API server.

#### Switching your config files to a new API version

You can use `kubectl convert` command to convert config files between different API versions.
