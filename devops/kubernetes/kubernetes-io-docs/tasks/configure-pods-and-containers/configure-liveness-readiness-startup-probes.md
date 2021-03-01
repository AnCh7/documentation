### Configure Liveness, Readiness and Startup Probes

> References:
>
> https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/



The [kubelet](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/) uses **liveness** probes to know when to restart a container.

The kubelet uses **readiness** probes to know when a container is ready to start accepting traffic. Pod is considered ready when all of its containers are ready. One use of this signal is to control which Pods are used as backends for Services. When a Pod is not ready, it is removed from Service load balancers.

The kubelet uses **startup** probes to know when a container application has started. If such a probe is configured, it disables liveness and readiness checks until it succeeds, making sure those probes don't interfere with the application startup. This can be used to adopt liveness checks on slow starting containers, avoiding them getting killed by the kubelet before they are up and running.

[Probes](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.20/#probe-v1-core) have a number of fields that you can use to more precisely control the behavior of liveness and readiness checks:

- `initialDelaySeconds`: Number of seconds after the container has started before liveness or readiness probes are initiated. Defaults to 0 seconds. Minimum is 0.
- `periodSeconds`: How often (in seconds) to perform the probe. Default to 10 seconds. Minimum is 1.
- `timeoutSeconds`: Number of seconds after which the probe times out. Defaults to 1 second. Minimum is 1.
- `successThreshold`: Minimum consecutive successes for the probe to be considered successful after having failed. Defaults to 1. Must be 1 for liveness and startup Probes. Minimum is 1.
- `failureThreshold`: When a probe fails, Kubernetes will try `failureThreshold` times before giving up. Giving up in  case of liveness probe means restarting the container. In case of  readiness probe the Pod will be marked Unready. Defaults to 3. Minimum is 1.

[HTTP probes](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.20/#httpgetaction-v1-core) have additional fields that can be set on `httpGet`:

- `host`: Host name to connect to, defaults to the pod IP. You probably want to set "Host" in httpHeaders instead.
- `scheme`: Scheme to use for connecting to the host (HTTP or HTTPS). Defaults to HTTP.
- `path`: Path to access on the HTTP server. Defaults to /.
- `httpHeaders`: Custom headers to set in the request. HTTP allows repeated headers.
- `port`: Name or number of the port to access on the container. Number must be in the range 1 to 65535.

