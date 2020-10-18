# How to attach a GCE disk to a pod

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
    name: disk-name
spec:
    storageClassName: ""
    capacity:
        storage: 10G
    accessModes:
        - ReadWriteOnce
    gcePersistentDisk:
        pdName: gce-disk-name
        fsType: ext4
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
    name: disk-name
spec:
    # It's necessary to specify "" as the storageClassName
    # so that the default storage class won't be used, see
    # https://kubernetes.io/docs/concepts/storage/persistent-volumes/#class-1
    storageClassName: ""
    volumeName: disk-name
    accessModes:
        - ReadWriteOnce
    resources:
        requests:
            storage: 10G
```