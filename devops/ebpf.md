### eBPF

> References:
>
> https://ebpf.io
>
> https://www.kernel.org/doc/html/latest/bpf/index.html

Extended Berkeley Packet Filter.

BPF allows a user-space program to attach a filter onto any socket and allow or disallow certain types of data to come through the socket.

The Linux kernel has always been an ideal place to implement monitoring/observability, networking, and security.  Unfortunately this was often impractical as it required changing kernel  source code or loading kernel modules, and resulted in layers of  abstractions stacked on top of each other. eBPF is a revolutionary  technology that can run sandboxed programs in the Linux kernel without  changing kernel source code or loading kernel modules.

By making  the Linux kernel programmable, infrastructure software can leverage  existing layers, making them more intelligent and feature-rich without  continuing to add additional layers of complexity to the system or  compromising execution efficiency and safety.

![img](.ebpf-images/go-1a1bb6f1e64b1ad5597f57dc17cf1350.png)

eBPF has resulted in the development of a completely new generation of  software able to reprogram the behavior of the Linux kernel and even  apply logic across multiple subsystems which were traditionally  completely independent.

- Security - eBPF allows for combining the visibility and control of all aspects to create security systems operating on more context with better level of control.
- Networking - The programmability of eBPF enables adding additional protocol parsers and easily program any forwarding logic to meet changing requirements without ever leaving the packet processing context of the Linux kernel.
- Tracing & Profiling - The ability to attach eBPF programs to trace points as well as kernel and user application probe points allows unprecedented visibility into the runtime behavior of applications and the system itself. 
- Observability & Monitoring - Instead of relying on static counters and gauges exposed by the operating system, eBPF enables the collection & in-kernel aggregation of custom metrics and generation of visibility events based on a wide range of possible sources