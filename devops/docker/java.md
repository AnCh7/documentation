### Memory

Docker uses control groups (cgroups) to limit resources but JVM sees the whole memory and all CPU cores available on the system and aligns its resources to it.

Docker switches (`-m, –memory` and `–memory-swap`) and the kubernetes switch (`–limits`) instruct the Linux kernel to kill the process if it tries to exceed the specified limit, but the JVM is completely unaware of the limits.

**Java 8 (before update 131)** - you needed to specify limits inside your container by setting `-Xmx` and `-Xms` flags to limit the heap size. Please note that setting `-Xmx` and `-Xms` disables the automatic heap sizing.

**Java 9+** and **8u131+** there are flags added to the JVM:  `-XX:+UnlockExperimentalVMOptions` and `-XX:+UseCGroupMemoryLimitForHeap`. These flags will force the JVM to look at the Linux cgroup configuration.

**Java 10+** these experimental flags are the new default and are enabled using the `-XX:+UseContainerSupport`.

