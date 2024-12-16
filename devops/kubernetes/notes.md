# Overview

```
graph TD
    subgraph KubernetesControlPlane
        KubeAPI[API Server]
        KubeScheduler[Scheduler]
        KubeController[Controller Manager]
        KubeETCD[etcd]
    end

    subgraph KubernetesNodes
        Node1[Node 1]

        Node1 -->|Hosts| Pod2(Pod)
        Node1 -->|Hosts| Pod(Pod)

        Node -->|Runs| DockerD
        Node -->|Managed by| Kubelet
        Node -->|Managed by| KubeProxy
    end

    KubeUI[Kubernetes UI]
    KubeCLI[Kubectl CLI]

    KubeUI -->|Interacts with| KubeAPI
    KubeCLI -->|Interacts with| KubeAPI
    KubeAPI -->|Manages| KubeScheduler
    KubeAPI -->|Manages| KubeController
    KubeAPI -->|Stores data in| KubeETCD
    KubeScheduler -->|Assigns| Node1
    KubeScheduler -->|Assigns| Node
    KubeController -->|Monitors| Node1
    KubeController -->|Monitors| Node

    KubeETCD -->|Stores data for| KubeAPI
```

### Kubernetes Control Plane

- KubeAPI (API Server): This is the entry point for controlling and managing the cluster. It exposes the Kubernetes API, allowing users and other components to interact with the cluster.
- KubeScheduler (Scheduler): The scheduler is responsible for making decisions about where to place pods (the smallest deployable units in Kubernetes) based on factors like resource requirements, affinity, and anti-affinity rules.
- KubeController (Controller Manager): The controller manager ensures that the desired state of the cluster is maintained. It includes various controllers like the Replication Controller and Endpoint Controller.
- KubeETCD (etcd): etcd is a distributed key-value store that stores configuration data, cluster state, and other critical information about the cluster.

### Kubernetes Nodes

- Node (Node): This represents the general concept of worker nodes in the cluster, which can host multiple pods. It also runs the container runtime (DockerD) and is managed by Kubelet and KubeProxy.