### Velero

Velero (formerly Heptio Ark) gives you tools to back up and restore your Kubernetes cluster resources and persistent volumes. 

Velero lets you:

- Take backups of your cluster and restore in case of loss.
- Migrate cluster resources to other clusters.
- Replicate your production cluster to development and testing clusters.

Velero consists of:

- A server that runs on your cluster
- A command-line client that runs locally

Server includes:

- A `velero` namespace.
- The `velero` service account.
- Role-based access control (RBAC) rules to grant permissions to the `velero` service account.
- Custom Resources (CRDs) for the velero-specific resources.
- Velero needs a S3 compatible object storage to store the backups.

#### How Velero Works

Each Velero operation (on-demand backup, scheduled backup, restore) is a custom resource, defined with a Kubernetes [Custom Resource Definition (CRD)](https://kubernetes.io/docs/concepts/api-extension/custom-resources/#customresourcedefinitions) and stored in [etcd](https://github.com/coreos/etcd). Velero also includes controllers that process the custom resources to perform backups, restores, and all related operations.

The on-demand backups operation:

1. Uploads a tarball of copied Kubernetes objects into cloud object storage.
2. Calls the cloud provider API to make disk snapshots of persistent volumes, if specified.

Cluster backups are not strictly atomic.

The **schedule** operation allows you to back up your data at recurring intervals.

The **restore** operation allows you to restore all of the objects and persistent volumes from a previously created backup.

----

#### Backup workflow

![19](.velero-images/backup-process.png)

1. The Velero client makes a call to the Kubernetes API server to create a `Backup` object.
2. The `BackupController` notices the new `Backup`object and performs validation.
3. The `BackupController` begins the backup process. It collects the data to back up by querying the API server for resources.
4. The `BackupController` makes a call to the object storage service – for example, AWS S3 – to upload the backup file.

Velero backs up resources using the Kubernetes API server's `preferred version` for each group/resource. When restoring a resource, this same API  group/version must exist in the target cluster in order for the restore  to be successful.

---

When you create a backup, you can specify a TTL by adding the flag `--ttl`. 

---

#### Locations

Velero has two custom resources, `BackupStorageLocation` and `VolumeSnapshotLocation`, that are used to configure where Velero backups and their associated persistent volume snapshots are stored.

```bash
velero snapshot-location create ebs-us-east-1 \
    --provider aws \
    --config region=us-east-1
    
velero backup create full-cluster-backup \
    --volume-snapshot-locations ebs-us-east-1
```

Velero can store backups in a number of locations. These are represented in the cluster via the `BackupStorageLocation` CRD.

```yaml
apiVersion: velero.io/v1
kind: BackupStorageLocation
metadata:
  name: default
  namespace: velero
spec:
  backupSyncPeriod: 2m0s
  provider: aws
  objectStorage:
    bucket: myBucket
  config:
    region: us-west-2
    profile: "default"
```

A volume snapshot location is the location in which to store the volume snapshots created for a backup.

Velero can be configured to take snapshots of volumes from multiple  providers. Velero also allows you to configure multiple possible `VolumeSnapshotLocation` per provider, although you can only select one location per provider at backup time.

Each VolumeSnapshotLocation describes a provider + location. These are represented in the cluster via the `VolumeSnapshotLocation` CRD. Velero must have at least one `VolumeSnapshotLocation` per cloud provider.

```yaml
apiVersion: velero.io/v1
kind: VolumeSnapshotLocation
metadata:
  name: aws-default
  namespace: velero
spec:
  provider: aws
  config:
    region: us-west-2
    profile: "default"
```

#### Providers

Velero supports a variety of storage providers for different backup and  snapshot operations. Velero has a plugin system which allows anyone to  add compatibility for additional backup and volume storage platforms  without modifying the Velero codebase.

#### Example

```bash
# Create a backup for any object that matches the app=nginx label selector:
velero backup create nginx-backup --selector app=nginx

# Restore
velero restore create --from-backup nginx-backup
velero restore get

# If there are errors or warnings, you can look at them in detail:
velero restore describe <RESTORE_NAME>

# If you want to delete any backups you created, including data in object storage and persistent volume snapshots:
velero backup delete BACKUP_NAME
```

#### Restic

Velero has support for backing up and restoring Kubernetes volumes using a free open-source backup tool called [restic](https://github.com/restic/restic).

If you're running on AWS, and taking EBS snapshots as part of your regular Velero backups, there's no need to switch to using restic. However, if you've been waiting for a snapshot plugin for your storage platform, or if you're using EFS, AzureFile, NFS, emptyDir, local, or any other volume type that doesn't have a native snapshot concept, restic might be for you.

To install restic, use the `--use-restic` flag on the `velero install` command.

---

#### Disaster recovery

```bash
# set up a daily backup
velero schedule create <SCHEDULE NAME> --schedule "0 7 * * *"

# update your backup storage location to read-only mode
kubectl patch backupstoragelocation <STORAGE LOCATION NAME> \
    --namespace velero \
    --type merge \
    --patch '{"spec":{"accessMode":"ReadOnly"}}'

# create a restore with your most recent backup
velero restore create --from-backup <SCHEDULE NAME>-<TIMESTAMP>

# revert your backup storage location to read-write mode
kubectl patch backupstoragelocation <STORAGE LOCATION NAME> \
       --namespace velero \
       --type merge \
       --patch '{"spec":{"accessMode":"ReadWrite"}}'
```

#### Cluster migration

```bash
# Cluster 1: back up your entire cluster
velero backup create <BACKUP-NAME>

# Cluster 2: Configure BackupStorageLocations and VolumeSnapshotLocations, pointing to the locations used by Cluster 1, using velero backup-location create and velero snapshot-location create. Make sure to configure the BackupStorageLocations as read-only by using the --access-mode=ReadOnly flag for velero backup-location create.

# Cluster 2: Make sure that the Velero Backup object is created
velero backup describe <BACKUP-NAME>

# Cluster 2: Once you have confirmed that the right backup is now present, you can restore everything with:
velero restore create --from-backup <BACKUP-NAME>

# Verify both clusters
velero restore get
velero restore describe <RESTORE-NAME-FROM-GET-COMMAND>
```

---

Velero can restore resources into a different namespace than the one they were backed up from. To do this, use the `--namespace-mappings` flag:

```bash
velero restore create RESTORE_NAME \
       --from-backup BACKUP_NAME \
       --namespace-mappings old-ns-1:new-ns-1,old-ns-2:new-ns-2
```

#### Hooks

Velero currently supports executing commands in containers in pods during a backup.

When performing a backup, you can specify one or more commands to execute in a container in a pod when that pod is being backed up. The commands can be configured to run *before* any custom action processing ("pre" hooks), or after all custom actions have been completed and any additional items specified by custom action have been backed up ("post" hooks). 

#### Velero plugin system

Velero uses storage provider plugins to integrate with a variety of storage systems to support backup and snapshot operations. Any plugin can be added after Velero has been installed by using the command `velero plugin add `.

#### Output file format

A backup is a gzip-compressed tar file whose name matches the Backup API resource's `metadata.name` (what is specified during `velero backup create `).

In cloud object storage, each backup file is stored in its own  subdirectory in the bucket specified in the Velero server configuration. This subdirectory includes an additional file called `velero-backup.json`. The JSON file lists all information about your associated Backup  resource, including any default values. This gives you a complete  historical record of the backup configuration. The JSON file also  specifies `status.version`, which corresponds to the output file format.

#### Install / Uninstall

* Install the CLI (brew, github)
* Install and configure the server components (helm or `velero install`)

Velero is installed in the `velero` namespace by default.

If you need to customize these resource requests and limits, you can set the flags in your `velero install` command.

Remove all resources created by `velero install`:

```bash
kubectl delete namespace/velero clusterrolebinding/velero
kubectl delete crds -l component=velero
```
