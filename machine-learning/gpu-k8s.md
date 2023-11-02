# GPU-powered Kubernetes clusters

> https://medium.com/bumble-tech/gpu-powered-kubernetes-clusters-7fc6505125c



Some situations when you might prefer CPU over GPU:

- Small RPS (Requests-per-second) inference without dynamic batching, where **transferring data** to GPU is taking more time than inference itself. 
- Real-time inference for algorithms that **don’t parallelise** easily. E.g. recurrent neural networks.
- Inference on **power-limited** devices such as phones and embedded computers.
- Some algorithms are optimized to use CPUs over GPUs.
- When you intend to train non-parallel (sequential/non-concurrent) algorithms
- When working with large datasets or large models (e.g. recommender system with large embedding layers).

Installation guide:

- Install Nvidia drivers on worker hosts

- Check that your device is visible via the nvidia-smi tool

- NVIDIA GPU Operator

  - k8s-device-plugin — Device Plugin for Kubernetes

  - container-toolkit — Set of tools to run GPU operated containers

  - dcgm-exporter — DCGM exporter used for monitoring and telemetry

  - gpu-feature-discovery — a component that allows you to automatically generate labels for the set of GPUs available on a node

  - mig-manager — component capable of repartitioning GPUs into different MIG configurations
  
- GPU scheduling, for example:

  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: cuda-vectoradd
  spec:
    containers:
    - name: cuda-vectoradd
      image: "nvidia/samples:vectoradd-cuda12.0.0"
      resources:
        limits:
          nvidia.com/gpu: 1
    nodeSelector:
      nvidia.com/gpu.product: NVIDIA-A100
  ```

- GPU monitoring

- Bin Packing - Kubernetes 1.24 introduces the MostAllocated strategy for bin packing. The MostAllocated strategy scores the nodes based on the utilisation of resources, favouring the ones with higher allocation.