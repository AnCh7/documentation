## Volumes

In Docker, a volume is simply a directory on disk or in another Container. 

A Kubernetes volume, on the other hand, has an explicit lifetime - the same as the Pod that encloses it. Consequently, a volume outlives any Containers that run within the Pod, and data is preserved across Container restarts. Of course, when a Pod ceases to exist, the volume will cease to exist, too. Perhaps more importantly than this, Kubernetes supports many types of volumes, and a Pod can use any number of them simultaneously.

To use a volume, a Pod specifies what volumes to provide for the Pod (the `.spec.volumes` field) and where to mount those into Containers (the `.spec.containers[*].volumeMounts` field).

Kubernetes supports several types of Volumes: [awsElasticBlockStore](https://kubernetes.io/docs/concepts/storage/volumes/#awselasticblockstore), [azureDisk](https://kubernetes.io/docs/concepts/storage/volumes/#azuredisk), [azureFile](https://kubernetes.io/docs/concepts/storage/volumes/#azurefile), [cephfs](https://kubernetes.io/docs/concepts/storage/volumes/#cephfs), [cinder](https://kubernetes.io/docs/concepts/storage/volumes/#cinder), [configMap](https://kubernetes.io/docs/concepts/storage/volumes/#configmap), [csi](https://kubernetes.io/docs/concepts/storage/volumes/#csi), [downwardAPI](https://kubernetes.io/docs/concepts/storage/volumes/#downwardapi), [emptyDir](https://kubernetes.io/docs/concepts/storage/volumes/#emptydir), [fc (fibre channel)](https://kubernetes.io/docs/concepts/storage/volumes/#fc), [flexVolume](https://kubernetes.io/docs/concepts/storage/volumes/#flexVolume), [flocker](https://kubernetes.io/docs/concepts/storage/volumes/#flocker), [gcePersistentDisk](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk), [gitRepo (deprecated)](https://kubernetes.io/docs/concepts/storage/volumes/#gitrepo), [glusterfs](https://kubernetes.io/docs/concepts/storage/volumes/#glusterfs), [hostPath](https://kubernetes.io/docs/concepts/storage/volumes/#hostpath), [iscsi](https://kubernetes.io/docs/concepts/storage/volumes/#iscsi), [local](https://kubernetes.io/docs/concepts/storage/volumes/#local), [nfs](https://kubernetes.io/docs/concepts/storage/volumes/#nfs), [persistentVolumeClaim](https://kubernetes.io/docs/concepts/storage/volumes/#persistentvolumeclaim), [projected](https://kubernetes.io/docs/concepts/storage/volumes/#projected), [portworxVolume](https://kubernetes.io/docs/concepts/storage/volumes/#portworxvolume), [quobyte](https://kubernetes.io/docs/concepts/storage/volumes/#quobyte), [rbd](https://kubernetes.io/docs/concepts/storage/volumes/#rbd), [scaleIO](https://kubernetes.io/docs/concepts/storage/volumes/#scaleio), [secret](https://kubernetes.io/docs/concepts/storage/volumes/#secret), [storageos](https://kubernetes.io/docs/concepts/storage/volumes/#storageos), [vsphereVolume](https://kubernetes.io/docs/concepts/storage/volumes/#vspherevolume)

---

The [`configMap`](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) resource provides a way to inject configuration data into Pods. The data stored in a `ConfigMap` object can be referenced in a volume of type `configMap` and then consumed by containerized applications running in a Pod.

---

An `emptyDir` volume is first created when a Pod is assigned to a Node, and exists as long as that Pod is running on that node. As the name says, it is initially empty. Containers in the Pod can all read and write the same files in the `emptyDir` volume, though that volume can be mounted at the same or different paths in each Container. When a Pod is removed from a node for any reason, the data in the `emptyDir` is deleted forever.

**Using subPath**

The `volumeMounts.subPath` property can be used to specify a sub-path inside the referenced volume instead of its root.

**Out-of-Tree Volume Plugins**

The Out-of-tree volume plugins include the Container Storage Interface (CSI) and FlexVolume. They enable storage vendors to create custom storage plugins without adding them to the Kubernetes repository.

Before the introduction of CSI and FlexVolume, all volume plugins (like volume types listed above) were “in-tree” meaning they were built, linked, compiled, and shipped with the core Kubernetes binaries and extend the core Kubernetes API. This meant that adding a new storage system to Kubernetes (a volume plugin) required checking code into the core Kubernetes code repository.

**Mount propagation**

Mount propagation allows for sharing volumes mounted by a Container to other Containers in the same Pod, or even to other Pods on the same node.

Mount propagation of a volume is controlled by `mountPropagation` field in Container.volumeMounts: None, HostToContainer, Bidirectional.


## Persistent Volumes

The `PersistentVolume` subsystem provides an API for users  and administrators that abstracts details of how storage is provided  from how it is consumed. 

A `PersistentVolume` (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using [Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/). It is a resource in the cluster just like a node is a cluster resource. PVs are volume plugins like Volumes, but have a lifecycle independent  of any individual Pod that uses the PV. This API object captures the  details of the implementation of the storage, be that NFS, iSCSI, or a  cloud-provider-specific storage system.

A `PersistentVolumeClaim` (PVC) is a request for storage by a user. It is similar to a Pod. Pods  consume node resources and PVCs consume PV resources. Pods can request  specific levels of resources (CPU and Memory). Claims can request  specific size and access modes (e.g., they can be mounted once  read/write or many times read-only).

### Lifecycle of a volume and claim

**Provisioning**

There are two ways PVs may be provisioned: statically or dynamically:
- Static
  A cluster administrator creates a number of PVs. They carry the details of the real storage, which is available for use by cluster users. They exist in the Kubernetes API and are available for consumption. 
- Dynamic
  When none of the static PVs the administrator created match a user’s `PersistentVolumeClaim`, the cluster may try to dynamically provision a volume specially for the PVC. This provisioning is based on `StorageClasses`: the PVC must request a [storage class](https://kubernetes.io/docs/concepts/storage/storage-classes/) and the administrator must have created and configured that class for dynamic provisioning to occur. Claims that request the class `""` effectively disable dynamic provisioning for themselves. The cluster administrator needs to enable the `DefaultStorageClass` [admission controller](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#defaultstorageclass) on the API server.

**Binding**

A control loop in the master watches for new PVCs, finds a matching PV  (if possible), and binds them together. If a PV was dynamically  provisioned for a new PVC, the loop will always bind that PV to the PVC. Otherwise, the user will always get at least what they asked for, but  the volume may be in excess of what was requested. Once bound, `PersistentVolumeClaim` binds are exclusive, regardless of how they were bound. A PVC to PV binding is a one-to-one mapping.

Claims will remain unbound indefinitely if a matching volume does not  exist. Claims will be bound as matching volumes become available.

**Using**

Pods use claims as volumes. The cluster inspects the claim to find  the bound volume and mounts that volume for a Pod. For volumes that  support multiple access modes, the user specifies which mode is desired  when using their claim as a volume in a Pod.

Once a user has a  claim and that claim is bound, the bound PV belongs to the user for as  long as they need it. Users schedule Pods and access their claimed PVs  by including a `persistentVolumeClaim` in their Pod’s volumes block. [See below for syntax details](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#claims-as-volumes).

**Storage Object in Use Protection**

The purpose of the Storage Object in Use Protection feature is to ensure that Persistent Volume Claims (PVCs) in active use by a Pod and  Persistent Volume (PVs) that are bound to PVCs are not removed from the  system, as this may result in data loss.

If a user deletes a PVC in active use by a Pod, the PVC is not removed  immediately. PVC removal is postponed until the PVC is no longer  actively used by any Pods. Also, if an admin deletes a PV that is bound  to a PVC, the PV is not removed immediately. PV removal is postponed  until the PV is no longer bound to a PVC.

**Reclaiming**

When a user is done with their volume, they can delete the PVC objects from the API that allows reclamation of the resource. The reclaim policy for a PersistentVolume tells the cluster what to do with the volume after it has been released of its claim:
- Retain - The `Retain` reclaim policy allows for manual reclamation of the resource. When the `PersistentVolumeClaim` is deleted, the `PersistentVolume` still exists and the volume is considered “released”. But it is not yet available for another claim because the previous claimant’s data  remains on the volume. An administrator can manually reclaim the volume.
- Delete - deletion removes both the PersistentVolume object from Kubernetes, as well as the associated storage asset in the external infrastructure, such as an AWS EBS etc. Volumes that were dynamically provisioned inherit the [reclaim policy of their `StorageClass`](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaim-policy), which defaults to `Delete`. The administrator should configure the `StorageClass` according to users’ expectations; otherwise, the PV must be edited or patched after it is created.
- Recycle - deprecated.

**Expanding Persistent Volumes Claims**

Support for expanding PersistentVolumeClaims (PVCs) is now enabled by default.  You can only expand a PVC if its storage class’s `allowVolumeExpansion` field is set to true.

**Persistent Volumes**

PersistentVolume types are implemented as plugins. 
Each PV contains a spec and status, which is the specification and status of the volume.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv0003
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: slow
  mountOptions:
    - hard
    - nfsvers=4.1
  nfs:
    path: /tmp
    server: 172.17.0.2
```

Capacity - a PV will have a specific storage capacity. This is set using the PV’s `capacity` attribute.

Volume Mode - you can set the value of `volumeMode` to `block` to use a raw block device, or `filesystem` to use a filesystem (default).

Access Modes - A PersistentVolume can be mounted on a host in any way supported by the resource provider:
- ReadWriteOnce – the volume can be mounted as read-write by a single node
- ReadOnlyMany – the volume can be mounted read-only by many nodes
- ReadWriteMany – the volume can be mounted as read-write by many nodes

Class - specified by setting the `storageClassName` attribute to the name of a [StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/).

Reclaim Policy - see above.

Mount Options - additional mount options.

Node Affinity - A PV can specify [node affinity](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#volumenodeaffinity-v1-core) to define constraints that limit what nodes this volume can be accessed from. Pods that use a PV will only be scheduled to nodes that are  selected by the node affinity.

Phase - A volume will be in one of the following phases:
- Available – a free resource that is not yet bound to a claim
- Bound – the volume is bound to a claim
- Released – the claim has been deleted, but the resource is not yet reclaimed by the cluster
- Failed – the volume has failed its automatic reclamation

**PersistentVolumeClaims**

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 8Gi
  storageClassName: slow
  selector:
    matchLabels:
      release: "stable"
    matchExpressions:
      - {key: environment, operator: In, values: [dev]}
```

Access Modes - same conventions as volumes.

Volume Modes - same convention as volumes.

Resources - request specific quantities of a storage.

Selector - can specify a [label selector](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#label-selectors) to further filter the set of volumes. Only the volumes whose labels match the selector can be bound to the claim.

Class - specifying the name of a [StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/).

**Claims As Volumes**

Pods access storage by using the claim as a volume. Claims must exist in the same namespace as the Pod using the claim. `PersistentVolumes` binds are exclusive, and since `PersistentVolumeClaims` are namespaced objects, mounting claims with “Many” modes (`ROX`, `RWX`) is only possible within one namespace.

**Raw Block Volume Support**

If a user requests a raw block volume by indicating this using the `volumeMode` field in the `PersistentVolumeClaim` spec, the binding rules differ slightly from previous releases that didn’t consider this mode as part of the spec.

**Volume Snapshot and Restore Volume from Snapshot Support**

Volume snapshot feature was added to support CSI Volume Plugins only.

**Volume Cloning**

Volume clone feature was added to support CSI Volume Plugins only.


## Volume Snapshots

A `VolumeSnapshotContent` is a snapshot taken from a volume in the cluster. It is a resource in the cluster just  like a PersistentVolume is a cluster resource. 

A `VolumeSnapshot` is a request for snapshot of a volume by a user. It is similar to a PersistentVolumeClaim.

`VolumeSnapshotClass` allows you to specify different attributes belonging to a `VolumeSnapshot`. These attibutes may differ among snapshots taken from the same volume  on the storage system and therefore cannot be expressed by using the  same `StorageClass` of a `PersistentVolumeClaim`.

Users need to be aware of the following when using this feature:
- API Objects `VolumeSnapshot`, `VolumeSnapshotContent`, and `VolumeSnapshotClass` are [CRDs ](https://kubernetes.io/docs/tasks/access-kubernetes-api/extend-api-custom-resource-definitions/), not part of the core API.
- `VolumeSnapshot` support is only available for CSI drivers.
- Kubernetes team provides a snapshot controller to be deployed into the  control plane, and a sidecar helper container called csi-snapshotter to  be deployed together with the CSI driver. The snapshot controller watches `VolumeSnapshot` and `VolumeSnapshotContent` objects and is responsible for the creation and deletion of `VolumeSnapshotContent` object in dynamic provisioning. The sidecar csi-snapshotter watches `VolumeSnapshotContent` objects and triggers `CreateSnapshot` and `DeleteSnapshot` operations against a CSI endpoint.
- CSI drivers may or may not have implemented the volume snapshot  functionality. The CSI drivers that have provided support for volume  snapshot will likely use the csi-snapshotter. See [CSI Driver documentation](https://kubernetes-csi.github.io/docs/) for details.
- The CRDs and snapshot controller installations are the responsibility of the Kubernetes distribution.

The interaction between `VolumeSnapshotContents` and `VolumeSnapshots` follow this lifecycle:
- Provisioning Volume Snapshot: There are two ways snapshots may be provisioned: pre-provisioned or dynamically provisioned.
  - Pre-provisioned - A cluster administrator creates a number of VolumeSnapshotContents. They carry the details of the real volume snapshot on the storage system which is available for use by cluster users. They exist in the Kubernetes API and are available for consumption
  - Dynamic - Instead of using a pre-existing snapshot, you can request that a  snapshot to be dynamically taken from a PersistentVolumeClaim. The [VolumeSnapshotClass](https://kubernetes.io/docs/concepts/storage/volume-snapshot-classes/) specifies storage provider-specific parameters to use when taking a snapshot.
- Binding - The snapshot controller handles the binding of a `VolumeSnapshot` object with an appropriate `VolumeSnapshotContent` object. The binding is a one-to-one mapping. In the case of  pre-provisioned binding, the VolumeSnapshot will remain unbound until  the requested VolumeSnapshotContent object is created.
- Persistent Volume Claim as Snapshot Source Protection - ensure that in-use PersistentVolumeClaim API objects are not removed  from the system while a snapshot is being taken from it (as this may  result in data loss).
- Delete - Deletion is triggered by deleting the `VolumeSnapshot` object, and the `DeletionPolicy` will be followed.

Example:
```yaml
apiVersion: snapshot.storage.k8s.io/v1beta1
kind: VolumeSnapshot
metadata:
  name: new-snapshot-test
spec:
  volumeSnapshotClassName: csi-hostpath-snapclass
  source:
    persistentVolumeClaimName: pvc-test
```

```yaml
apiVersion: snapshot.storage.k8s.io/v1beta1
kind: VolumeSnapshotContent
metadata:
  name: snapcontent-72d9a349-aacd-42d2-a240-d775650d2455
spec:
  deletionPolicy: Delete
  driver: hostpath.csi.k8s.io
  source:
    volumeHandle: ee0cfb94-f8d4-11e9-b2d8-0242ac110002
  volumeSnapshotClassName: csi-hostpath-snapclass
  volumeSnapshotRef:
    name: new-snapshot-test
    namespace: default
    uid: 72d9a349-aacd-42d2-a240-d775650d2455
```

---

You can provision a new volume, pre-populated with data from a snapshot, by using the *dataSource* field in the `PersistentVolumeClaim` object.


## CSI Volume Cloning

The [CSI](https://kubernetes.io/docs/concepts/storage/volumes/#csi) Volume Cloning feature adds support for specifying existing [PVC ](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)s in the `dataSource` field to indicate a user would like to clone a [Volume ](https://kubernetes.io/docs/concepts/storage/volumes/).

The implementation of cloning, from the perspective of the Kubernetes  API, simply adds the ability to specify an existing PVC as a dataSource  during new PVC creation.

Users need to be aware of the following when using this feature:
- Cloning support (`VolumePVCDataSource`) is only available for CSI drivers.
- Cloning support is only available for dynamic provisioners.
- CSI drivers may or may not have implemented the volume cloning functionality.
- You can only clone a PVC when it exists in the same namespace as the  destination PVC (source and destination must be in the same namespace).
- Cloning is only supported within the same Storage Class.
  - Destination volume must be the same storage class as the source
  - Default storage class can be used and storageClassName omitted in the spec


## Storage Classes

Different classes might map to quality-of-service levels, or to backup policies, or to arbitrary policies determined by the cluster administrators. Kubernetes itself is unopinionated about what classes represent.

Each `StorageClass` contains the fields `provisioner`, `parameters`, and `reclaimPolicy`, which are used when a `PersistentVolume` belonging to the class needs to be dynamically provisioned.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standard
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
reclaimPolicy: Retain
allowVolumeExpansion: true
mountOptions:
  - debug
volumeBindingMode: Immediate
```

Persistent Volumes that are dynamically created by a storage class will have the reclaim policy specified in the `reclaimPolicy` field of the class, which can be either `Delete` or `Retain`.

Persistent Volumes can be configured to be expandable. This feature when set to `true`, allows the users to resize the volume by editing the corresponding PVC object.

Persistent Volumes that are dynamically created by a storage class will have the mount options specified in the `mountOptions` field of the class.

The `volumeBindingMode` field controls when [volume binding and dynamic provisioning](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#provisioning) should occur (can be Immediate or WaitForFirstConsumer).

Storage classes have parameters that describe volumes belonging to the storage class. Different parameters may be accepted depending on the `provisioner`.


## Volume Snapshot Classes

Just like `StorageClass` provides a way for administrators to describe the “classes” of storage they offer when provisioning a volume, `VolumeSnapshotClass` provides a way to describe the “classes” of storage when provisioning a volume snapshot.

Each `VolumeSnapshotClass` contains the fields `driver`, `deletionPolicy`, and `parameters`, which are used when a `VolumeSnapshot` belonging to the class needs to be dynamically provisioned.


## Dynamic Volume Provisioning

Dynamic volume provisioning allows storage volumes to be created on-demand. Without dynamic provisioning, cluster administrators have to manually make calls to their cloud or storage provider to create new storage volumes, and then create [`PersistentVolume` objects](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) to represent them in Kubernetes.

The implementation of dynamic volume provisioning is based on the API object `StorageClass` from the API group `storage.k8s.io`. A cluster administrator can define as many `StorageClass` objects as needed, each specifying a volume plugin (aka provisioner) that provisions a volume and the set of parameters to pass to that provisioner when provisioning.
