# Concepts

> References:
> https://istio.io/latest/docs/concepts/

## Traffic Management

Istio’s traffic management model relies on the Envoy proxies that are deployed along with your services. All traffic that your mesh services send and receive (data plane traffic) is proxied through Envoy, making it easy to direct and control traffic around your mesh without making any changes to your services.

---

**Envoy**

Is the high-performance proxy that Istio uses to mediate inbound and outbound traffic for all [services](https://istio.io/docs/reference/glossary/#service) in the [service mesh](https://istio.io/docs/reference/glossary/#service-mesh). [Learn more about Envoy](https://envoyproxy.github.io/envoy/).

---

**Data plane**

Is the part of the mesh that directly controls communication between workload instances. Istio’s data plane uses intelligent [Envoy](https://istio.io/docs/reference/glossary/#envoy) proxies deployed as sidecars to mediate and control all traffic that your mesh services send and receive.

---

To populate its own service registry, Istio connects to a service discovery system. For example, if you’ve installed Istio on a Kubernetes cluster, then Istio automatically detects the services and endpoints in that cluster.

---

**Service registry**

Istio maintains an internal service registry containing the set of [services](https://istio.io/docs/reference/glossary/#service), and their corresponding [service endpoints](https://istio.io/docs/reference/glossary/#service-endpoint), running in a service mesh. Istio uses the service registry to generate [Envoy](https://istio.io/docs/reference/glossary/#envoy) configuration.

Istio does not provide [service discovery](https://en.wikipedia.org/wiki/Service_discovery), although most services are automatically added to the registry by [Pilot](https://istio.io/docs/reference/glossary/#pilot) adapters that reflect the discovered services of the underlying platform (Kubernetes, Consul, plain DNS). Additional services can also be registered manually using a [`ServiceEntry`](https://istio.io/docs/concepts/traffic-management/#service-entries) configuration.

---

Using this service registry, the Envoy proxies can then direct traffic to the relevant services. It distributes traffic across each service’s load balancing pool using a round-robin model.

Like other Istio configuration, the API is specified using Kubernetes custom resource definitions (CRDs).

### Virtual services

Lets you configure how requests are routed to a service within an Istio service mesh. Each virtual service consists of a set of routing rules that are evaluated in order, letting Istio match each given request to the virtual service to a specific real destination within the mesh. 
