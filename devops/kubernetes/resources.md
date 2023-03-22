## Best practices for limits and requests

> https://home.robusta.dev/blog/stop-using-cpu-limits
>
> https://www.reddit.com/r/kubernetes/comments/wgztqh/for_the_love_of_god_stop_using_cpu_limits_on
>
> https://erickhun.com/posts/kubernetes-faster-services-no-cpu-limits

Pods always get the CPU requested by their CPU request.

They can take advantage of excess CPU too if they have no limit.

![img](.resources-images/6357fcc4b3a1634d362a408a_CPU%20Limits.webp)



#### Best practices for CPU limits and requests on Kubernetes

1. Use CPU requests for everything
2. Make sure they are accurate
3. Do **not** use CPU limits.



#### Best practices for Memory limits and requests on Kubernetes

1. Always use memory limits

2. Always use memory requests

3. Always set your memory requests equal to your limits

   

---

When you request "2 cores", we do not reserve 2 cores for you and leave them idle until you need them.  In fact, you didn't request "2 cores",  you requested "2.0 CPU seconds per second" or "2000 CPU milliseconds per second".

---

CPU limits for each workload should be set *significantly higher* than what the application is expected to use on a normal basis, but  within some sane limit.  In our clusters, for example, every Pod is  expected to live within the constraint of 16GiB RAM and 4 hyperthreads  (2 cores).  In this context, forcing all Pods to set a CPU limit of no  more than 4 CPUs is expected and actually *improves* the health of cluster overall, by throttling otherwise disruptive workloads.

---

Kubernetes use a mechanism called [CFS Quota](https://en.wikipedia.org/wiki/Completely_Fair_Scheduler) to **throttle** the container to prevent the CPU usage from going above the limit. That means CPU will be artificially restricted, making the performance of  your containers lower (and slower when it comes to latency).

---

**Why do we see CPU throttling while CPU usage is low?** The tldr is basically a bug in the Linux kernel throttling unecessarly containers with CPU limit. The bug has been fixed and merged into the kernel for Linux distribution running 4.19 or higher. However, as for September 2nd 2020, when reading the kubernetes issue, we can see various Linux projects that keep referencing the issue, so I guess some Linux distribution still have the bug and working into integrating the fix.



