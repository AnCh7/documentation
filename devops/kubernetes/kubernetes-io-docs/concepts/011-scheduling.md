## Kubernetes Scheduler

In Kubernetes, scheduling refers to making sure that [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) are matched to [Nodes](https://kubernetes.io/docs/concepts/architecture/nodes/) so that [Kubelet](https://kubernetes.io/docs/reference/generated/kubelet) can run them.

A scheduler watches for newly created Pods that have no Node assigned. For every Pod that the scheduler discovers, the scheduler becomes responsible for finding the best Node for that Pod to run on. The scheduler reaches this placement decision taking into account the scheduling principles described below.

[kube-scheduler](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/) is the default scheduler for Kubernetes and runs as part of the [control plane ](https://kubernetes.io/docs/reference/glossary/?all=true#term-control-plane).

For every newly created pod or other unscheduled pods, kube-scheduler selects an optimal node for them to run on. However, every container in pods has different requirements for resources and every pod also has different requirements. Therefore, existing nodes need to be filtered according to the specific scheduling requirements.

In a cluster, Nodes that meet the scheduling requirements for a Pod are called feasible nodes. If none of the nodes are suitable, the pod remains unscheduled until the scheduler is able to place it.

The scheduler finds feasible Nodes for a Pod and then runs a set of functions to score the feasible Nodes and picks a Node with the highest score among the feasible ones to run the Pod. The scheduler then notifies the API server about this decision in a process called binding.

Factors that need taken into account for scheduling decisions include individual and collective resource requirements, hardware / software / policy constraints, affinity and anti-affinity specifications, data locality, inter-workload interference, and so on.

kube-scheduler selects a node for the pod in a 2-step operation:

1. Filtering - finds the set of Nodes where it’s feasible to schedule the Pod. For example, the PodFitsResources filter checks whether a candidate Node has enough available resource to meet a Pod’s specific resource requests. After this step, the node list contains any suitable Nodes; often, there will be more than one. If the list is empty, that Pod isn’t (yet) schedulable. Some policies: `PodFitsHostPorts, PodFitsHost, PodFitsResources, NoDiskConflict etc.`
2. Scoring - the scheduler ranks the remaining nodes to choose the most suitable Pod placement. The scheduler assigns a score to each Node that survived filtering, basing this score on the active scoring rules. Some policies: `SelectorSpreadPriority, InterPodAffinityPriority, LeastRequestedPriority, etc.`

Finally, kube-scheduler assigns the Pod to the Node with the highest ranking. If there is more than one node with equal scores, kube-scheduler selects one of these at random.


## Scheduler Performance Tuning

Kubernetes 1.12 added a new feature that allows the scheduler to stop looking for more feasible nodes once it finds a certain number of them. This improves the scheduler’s performance in large clusters. The number is specified as a percentage of the cluster size. The percentage can be controlled by a configuration option called `percentageOfNodesToScore`. The range should be between 1 and 100. Larger values are considered as 100%. Zero is equivalent to not providing the config option. Kubernetes 1.14 has logic to find the percentage of nodes to score based on the size of the cluster if it is not specified in the configuration. It uses a linear formula which yields 50% for a 100-node cluster. The formula yields 10% for a 5000-node cluster. The lower bound for the automatic value is 5%. In other words, the scheduler always scores at least 5% of the cluster no matter how large the cluster is, unless the user provides the config option with a value smaller than 5.

In order to give all the Nodes in a cluster a fair chance of being considered for running Pods, the scheduler iterates over the nodes in a round robin fashion. You can imagine that Nodes are in an array. The scheduler starts from the start of the array and checks feasibility of the nodes until it finds enough Nodes as specified by `percentageOfNodesToScore`. For the next Pod, the scheduler continues from the point in the Node array that it stopped at when checking feasibility of Nodes for the previous Pod.

If Nodes are in multiple zones, the scheduler iterates over Nodes in various zones to ensure that Nodes from different zones are considered in the feasibility checks.
