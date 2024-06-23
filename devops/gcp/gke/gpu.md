# GPU sharing strategies

> https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/container_cluster#nested_guest_accelerator
>
> https://docs.nvidia.com/datacenter/tesla/mig-user-guide/index.html#partitioning
>
> https://cloud.google.com/kubernetes-engine/docs/concepts/gpus

- **Multi-instance GPU**: GKE divides a single supported GPU in up to seven slices. Each slice can be allocated to one container on the node independently, for a maximum of seven containers per GPU. Multi-instance GPU provides hardware isolation between the workloads, plus consistent and predictable Quality of Service (QoS) for all containers running on the GPU.
- **GPU time-sharing**: GKE uses the built-in timesharing ability provided by the NVIDIA GPU and the software stack. Starting with the [Pascal architecture](https://www.nvidia.com/en-us/data-center/pascal-gpu-architecture/), NVIDIA GPUs support instruction level preemption. When doing context switching between processes running on a GPU, instruction-level preemption ensures every process gets a fair timeslice. GPU time-sharing provides software-level isolation between the workloads in terms of address space isolation, performance isolation, and error isolation.
- **NVIDIA MPS**: GKE uses [NVIDIA's Multi-Process Service (MPS)](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf). NVIDIA MPS is an alternative, binary-compatible implementation of the CUDA API designed to transparently enable co-operative multi-process CUDA workloads to run concurrently on a single GPU device. GPU with NVIDIA MPS provides software-level isolation in terms of resource limits ([active thread percentage](https://docs.nvidia.com/deploy/mps/index.html#topic_5_2_5) and [pinned device memory](https://docs.nvidia.com/deploy/mps/index.html#topic_5_2_7)).

#### Which GPU sharing strategy to use

|                              | Multi-instance GPU                                           | GPU time-sharing                                             | NVIDIA MPS                                                   |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| General                      | Parallel GPU sharing among containers                        | Rapid context switching                                      | Parallel GPU sharing among containers                        |
| Isolation                    | A single GPU is divided in up to seven slices and    each container on the same physical GPU has dedicated compute, memory, and    bandwidth. Therefore, a container in a partition has a predictable    throughput and latency even when other containers saturate other    partitions. | Each container accesses the full capacity of the underlying physical      GPU by doing context switching between processes running on a GPU. However, time-sharing provides no memory limit enforcement between shared Jobs      and the rapid context switching for shared access may introduce      overhead. | NVIDIA MPS has limited resource isolation, but gains more    flexibility in other dimensions, for example GPU types and max shared units,    which simplify resource allocation. |
| Suitable for these workloads | Recommended for workloads running    in parallel and that need certain resiliency and QoS. For example, when running    AI inference workloads, multi-instance GPU multi-instance GPU allows    multiple inference queries to run simultaneously for quick responses,    without slowing each other down. | Recommended for bursty and interactive workloads that have idle periods. These workloads are not cost-effective with a fully dedicated GPU. By using time-sharing, workloads get quick access to the GPU when they are in active phases. GPU time-sharing is optimal for scenarios to avoid idling costly GPUs where full isolation and continuous      GPU access might not be necessary, for example, when multiple users test or prototype workloads.       Workloads that use      time-sharing need to tolerate certain performance and latency      compromises. | Recommended for batch processing for small jobs because MPS maximizes      the throughput and concurrent use of a GPU. MPS allows batch jobs to      efficiently process in parallel for small to medium sized workloads.      NVIDIA MPS is optimal for cooperative processes acting as a single      application. For example, MPI jobs with inter-MPI rank parallelism. With these jobs,      each small CUDA process (typically MPI ranks) can run      concurrently on the GPU to fully saturate the whole GPU.      Workloads that use CUDA MPS need to tolerate the [memory protection and error containment limitations](https://docs.nvidia.com/deploy/mps/#topic_3_3_3). |
| Monitoring                   | GPU utilization metrics are not available for    multi-instance GPUs. | [Cloud Monitoring](https://cloud.google.com/monitoring/docs) | [Cloud Monitoring](https://cloud.google.com/monitoring/docs) and [Monitor GPU time-sharing or NVIDIA MPS nodes](https://cloud.google.com/kubernetes-engine/docs/concepts/timesharing-gpus#monitor). |

You can combine the GPU sharing strategies.

### How the GPU sharing strategies work

> TLDR: nodes with GPUs attached and labels + pods with labels.

You can specify the maximum number of containers allowed to share a physical GPU:

- On Autopilot clusters, this is configured in your workload specification.
- On Standard clusters, this is configured when you create a new node pool with GPUs attached. Every GPU in the node pool is shared based on the setting you specify at the node pool level.

#### Multi-instance GPU

You can request multi-instance GPU in workloads by specifying the `cloud.google.com/gke-gpu-partition-size` label in the Pod spec `nodeSelector` field, under `spec: nodeSelector`.

#### GPU time-sharing or NVIDIA MPS

You can request GPU time-sharing or NVIDIA MPS in workloads by specifying the following labels in the Pod spec `nodeSelector` field, under `spec:nodeSelector`.

- `cloud.google.com/gke-max-shared-clients-per-gpu`: Select nodes that allow a specific number of clients to share the underlying GPU.
- `cloud.google.com/gke-gpu-sharing-strategy`: Select nodes that use the time-sharing or NVIDIA MPS strategy for GPUs.

You can only request one GPU for each container. GKE rejects a request for more than one GPU in a container to avoid unexpected behavior. The number of GPUs requested with time-sharing and NVIDIA MPS is not a measure of the compute power available to the container.

## Running multi-instance GPUs

Multi-instance GPUs allow you to partition a single supported GPU in up to **seven** slices. Each slice can be allocated to one container on the node independently, for a maximum of seven containers per GPU.

Multi-instance GPUs provide hardware isolation between the workloads, and consistent and predictable QoS for all containers running on the GPU.

For [CUDA](https://developer.nvidia.com/cuda-zone)® applications, multi-instance GPUs are largely transparent. Each GPU partition appears as a regular GPU resource, and the programming model remains unchanged.

The following GPU types support multi-instance GPUs:

- NVIDIA A100 (40GB)
- NVIDIA A100 (80GB)
- NVIDIA H100 (80GB)

The A100 GPU and H100 GPU consist of seven compute units and eight memory units, which can be partitioned into GPU instances of varying sizes. The GPU partition sizes use the following syntax:  `[compute]g.[memory]gb`. For example, a GPU partition size of `1g.5gb` refers to a GPU instance with one compute unit (1/7th of streaming multiprocessors on the GPU), and one memory unit (5 GB). 

| Partition size                                | GPU instances |
| --------------------------------------------- | ------------- |
| GPU: NVIDIA A100 (40GB) (`nvidia-tesla-a100`) |               |
| `1g.5gb`                                      | 7             |
| `2g.10gb`                                     | 3             |
| `3g.20gb`                                     | 2             |
| `7g.40gb`                                     | 1             |

Each GPU on each node within a node pool is partitioned the same way.

Create a cluster with multi-instance GPUs enabled:

```shell
gcloud container clusters create CLUSTER_NAME \
    --project=PROJECT_ID \
    --zone ZONE \
    --cluster-version=CLUSTER_VERSION \
    --accelerator type=nvidia-tesla-a100,count=1,gpu-partition-size=1g.5gb,gpu-driver-version=DRIVER_VERSION \
    --machine-type=a2-highgpu-1g \
    --num-nodes=1
```

Requests a single GPU with partition size `1g.5gb`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cuda-simple
spec:
  replicas: 7
  selector:
    matchLabels:
      app: cuda-simple
  template:
    metadata:
      labels:
        app: cuda-simple
    spec:
      nodeSelector:
        cloud.google.com/gke-gpu-partition-size: 1g.5gb
      containers:
      - name: cuda-simple
        image: nvidia/cuda:11.0.3-base-ubi7
        command:
        - bash
        - -c
        - |
          /usr/local/nvidia/bin/nvidia-smi -L; sleep 300
        resources:
          limits:
            nvidia.com/gpu: 1
```

## Share GPUs with multiple workloads using GPU time-sharing

You can enable GPU time-sharing on all [NVIDIA GPU models](https://cloud.google.com/compute/docs/gpus).

```shell
gcloud container clusters create CLUSTER_NAME \
    --region=COMPUTE_REGION \
    --cluster-version=CLUSTER_VERSION \
    --machine-type=MACHINE_TYPE \
    --accelerator=type=GPU_TYPE,count=GPU_QUANTITY,gpu-sharing-strategy=time-sharing,max-shared-clients-per-gpu=CLIENTS_PER_GPU,gpu-driver-version=DRIVER_VERSION
```

Deploy workloads:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cuda-simple
spec:
  replicas: 3
  selector:
    matchLabels:
      app: cuda-simple
  template:
    metadata:
      labels:
        app: cuda-simple
    spec:
      nodeSelector:
        cloud.google.com/gke-gpu-sharing-strategy: "SHARING_STRATEGY"
        cloud.google.com/gke-max-shared-clients-per-gpu: "CLIENTS_PER_GPU"
      containers:
      - name: cuda-simple
        image: nvidia/cuda:11.0.3-base-ubi7
        command:
        - bash
        - -c
        - |
          /usr/local/nvidia/bin/nvidia-smi -L; sleep 300
        resources:
          limits:
            nvidia.com/gpu: 1
```

##### Use GPU time-sharing with multi-instance GPUs

You can configure GPU time-sharing for each multi-instance GPU partition. For example, if you set the `gpu-partition-size` to `1g.5gb`, the underlying GPU would be split into `7` partitions. If you also set `max-shared-clients-per-gpu` to `3`, each partition would support up to three containers, for a total of up  to `21` GPU time-sharing devices available to allocate in that physical  GPU.

To avoid running into out-of-memory (OOM) issues, set GPU memory limits in your workloads. 

The maximum number of containers that can use time-sharing in a single physical GPU is **48**.

## Share GPUs with multiple workloads using NVIDIA MPS

TODO.