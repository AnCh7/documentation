## EndpointSlices

EndpointSlices provide a simple way to track network endpoints within a Kubernetes cluster. They offer a more scalable and extensible alternative to Endpoints.

EndpointSlice is considered “full” once it reaches 100 endpoints, at which point additional EndpointSlices will be created to store any additional endpoints.

They will include references to any Pods that match the Service selector. EndpointSlices group network endpoints together by unique Service and Port combinations.

Sample EndpointSlice resource for the `example` Kubernetes Service.

```yaml
apiVersion: discovery.k8s.io/v1beta1
kind: EndpointSlice
metadata:
  name: example-abc
  labels:
    kubernetes.io/service-name: example
addressType: IPv4
ports:
  - name: http
    protocol: TCP
    port: 80
endpoints:
  - addresses:
      - "10.1.2.3"
    conditions:
      ready: true
    hostname: pod-1
    topology:
      kubernetes.io/hostname: node-1
      topology.kubernetes.io/zone: us-west2-a
```

EndpointSlices support three address types:
- IPv4
- IPv6
- FQDN (Fully Qualified Domain Name)

Each endpoint within an EndpointSlice can contain relevant `topology` information. This is used to indicate where an endpoint is, containing information about the corresponding node, zone, and region.

By default, EndpointSlices are created and managed by the EndpointSlice controller. To ensure that multiple entities can manage EndpointSlices without interfering with each other, a `endpointslice.kubernetes.io/managed-by` label is used to indicate the entity managing an EndpointSlice. 

The EndpointSlice controller watches Services and Pods to ensure corresponding EndpointSlices are up to date. The controller will manage EndpointSlices for every Service with a selector specified. These will represent the IPs of Pods matching the Service selector.

Each EndpointSlice has a set of ports that applies to all endpoints within the resource. When named ports are used for a Service, Pods may end up with different target port numbers for the same named port, requiring different EndpointSlices. 

The controller tries to fill EndpointSlices as full as possible, but does not actively rebalance them.


## Service

An abstract way to expose an application running on a set of [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) as a network service. 

In Kubernetes, a Service is an abstraction which defines a logical set of Pods and a policy by which to access them. The set of Pods targeted by a Service is usually determined by a [selector](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/).

This specification creates a new Service object named “my-service”, which targets TCP port 9376 on any Pod with the `app=MyApp` label:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

Kubernetes assigns this Service an IP address (ClusterIP), which is used by the Service proxies.

The controller for the Service selector continuously scans for Pods that match its selector, and then POSTs any updates to an Endpoint object also named “my-service”.

Port definitions in Pods have names, and you can reference these names in the `targetPort` attribute of a Service.

The default protocol for Services is TCP; you can also use any other [supported protocol](https://kubernetes.io/docs/concepts/services-networking/service/#protocol-support). Kubernetes supports multiple port definitions on a Service object. Each port definition can have the same `protocol`, or a different one.

**Services without selectors**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

Because this Service has no selector, the corresponding Endpoint object is not created automatically. You can manually map the Service to the network address and port where it’s running, by adding an Endpoint object manually.

```yaml
apiVersion: v1
kind: Endpoints
metadata:
  name: my-service
subsets:
  - addresses:
      - ip: 192.0.2.42
    ports:
      - port: 9376
```

**Virtual IPs and service proxies**

Every node in a Kubernetes cluster runs a `kube-proxy`. `kube-proxy` is responsible for implementing a form of virtual IP for `Services` of type other than [`ExternalName`](https://kubernetes.io/docs/concepts/services-networking/service/#externalname).

- User space proxy mode

  kube-proxy watches the Kubernetes master for the addition and removal of Service and Endpoint objects. For each Service it opens a port (randomly chosen) on the local node. Any connections to this “proxy port” is proxied to one of the Service’s backend Pods (as reported via Endpoints). kube-proxy takes the `SessionAffinity` setting of the Service into account when deciding which backend Pod to use. user-space proxy installs iptables rules which capture traffic to the Service’s `clusterIP` (which is virtual) and `port`. The rules redirect that traffic to the proxy port which proxies the backend Pod. By default, kube-proxy in userspace mode chooses a backend via a round-robin algorithm.

  <img src=".006-services-images/services-userspace-overview.svg" alt="Services overview diagram for userspace proxy"/>

- Iptables proxy mode

  kube-proxy watches the Kubernetes control plane for the addition and removal of Service and Endpoint objects. For each Service, it installs iptables rules, which capture traffic to the Service’s `clusterIP` and `port`, and redirect that traffic to one of the Service’s backend sets. For each Endpoint object, it installs iptables rules which select a backend Pod. By default, kube-proxy in iptables mode chooses a backend at random.

  Using iptables to handle traffic has a lower system overhead, because traffic is handled by Linux netfilter without the need to switch between userspace and the kernel space.

  ![Services overview diagram for iptables proxy](.006-services-images/services-iptables-overview.svg)

- IPVS proxy mode

  In `ipvs` mode, kube-proxy watches Kubernetes Services and Endpoints, calls `netlink` interface to create IPVS rules accordingly and synchronizes IPVS rules with Kubernetes Services and Endpoints periodically. This control loop ensures that IPVS status matches the desired state. When accessing a Service, IPVS directs traffic to one of the backend Pods.

  IPVS provides more options for balancing traffic to backend Pods.

  ![Services overview diagram for IPVS proxy](.006-services-images/services-ipvs-overview.svg)

**Multi-Port Services**

When using multiple ports for a Service, you must give all of your ports names so that these are unambiguous.

**Choosing your own IP address**

You can specify your own cluster IP address as part of a `Service` creation request. To do this, set the `.spec.clusterIP` field.

**Discovering services**

Kubernetes supports 2 primary modes of finding a Service - environment variables and DNS.

- Environment variables

  When a Pod is run on a Node, the kubelet adds a set of environment variables for each active Service. 

  For example, the Service `"redis-master"` which exposes TCP port 6379 and has been allocated cluster IP address 10.0.0.11, produces the following environment variables:

```shell
REDIS_MASTER_SERVICE_HOST=10.0.0.11
REDIS_MASTER_SERVICE_PORT=6379
REDIS_MASTER_PORT=tcp://10.0.0.11:6379
REDIS_MASTER_PORT_6379_TCP=tcp://10.0.0.11:6379
REDIS_MASTER_PORT_6379_TCP_PROTO=tcp
REDIS_MASTER_PORT_6379_TCP_PORT=6379
REDIS_MASTER_PORT_6379_TCP_ADDR=10.0.0.11
```

  You must create the Service before the client Pods come into existence. Otherwise, those client Pods won’t have their environment variables populated.

- DNS

  You can set up a DNS service for your Kubernetes cluster using an [add-on](https://kubernetes.io/docs/concepts/cluster-administration/addons/). A cluster-aware DNS server, such as CoreDNS, watches the Kubernetes API for new Services and creates a set of DNS records for each one.

**Headless Services**

Specifying "None" for the cluster IP (.spec.clusterIP). For headless `Services`, a cluster IP is not allocated, kube-proxy does not handle these Services, and there is no load balancing or proxying done by the platform for them.

For headless Services that define selectors, the endpoints controller creates `Endpoints` records in the API, and modifies the DNS configuration to return records (addresses) that point directly to the `Pods` backing the `Service`.

For headless Services that do not define selectors, the endpoints controller does not create `Endpoints` records. However, the DNS system looks for and configures either:
- CNAME records for [`ExternalName`](https://kubernetes.io/docs/concepts/services-networking/service/#externalname)-type Services.
- A records for any `Endpoints` that share a name with the Service, for all other types.

**Publishing Services (ServiceTypes)**

Type values and their behaviors are:
- ClusterIP: Exposes the Service on a cluster-internal IP. Choosing this value makes the Service only reachable from within the cluster. This is the default `ServiceType`.
- [NodePort](https://kubernetes.io/docs/concepts/services-networking/service/#nodeport): Exposes the Service on each Node’s IP at a static port (the `NodePort`). A `ClusterIP` Service, to which the `NodePort` Service routes, is automatically created. You’ll be able to contact the `NodePort` Service, from outside the cluster, by requesting `:`.
- [LoadBalancer](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer): Exposes the Service externally using a cloud provider’s load balancer. `NodePort` and `ClusterIP` Services, to which the external load balancer routes, are automatically created.
- [ExternalName](https://kubernetes.io/docs/concepts/services-networking/service/#externalname): Maps the Service to the contents of the `externalName` field (e.g. `foo.bar.example.com`), by returning a `CNAME` record with its value. No proxying of any kind is set up.

**Virtual IP implementation**

To ensure each Service receives a unique IP, an internal allocator atomically updates a global allocation map in [etcd](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/) prior to creating each Service.

Service IPs are not actually answered by a single host. Instead, kube-proxy uses iptables (packet processing logic in Linux) to define *virtual* IP addresses which are transparently redirected as needed. kube-proxy supports three proxy modes—userspace, iptables and IPVS:

1. Userspace: when a proxy sees a new Service, it opens a new random port, establishes an iptables redirect from the virtual IP address to this new port. This means that Service owners can choose any port they want without risk of collision. Clients can simply connect to an IP and port, without being aware of which Pods they are actually accessing.
2. Iptables: When a proxy sees a new Service, it installs a series of iptables rules which redirect from the virtual IP address to per-Service rules. Unlike the userspace proxy, packets are never copied to userspace, the kube-proxy does not have to be running for the virtual IP address to work, and Nodes see traffic arriving from the unaltered client IP address.
3. IPVS: IP Virtual Server is designed for load balancing and based on in-kernel hash tables.

**Supported protocols**

- TCP - use TCP for any kind of Service, and it’s the default network protocol.
- UDP - use UDP for most Services. For type=LoadBalancer Services, UDP support depends on the cloud provider offering this facility.
- HTTP - if your cloud provider supports it, you can use a Service in LoadBalancer mode to set up external HTTP / HTTPS reverse proxying, forwarded to the Endpoints of the Service.
- PROXY protocol - If your cloud provider supports it (eg, [AWS](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#aws)), you can use a Service in LoadBalancer mode to configure a load balancer outside of Kubernetes itself.
- SCTP


## Service Topology

Service Topology enables a service to route traffic based upon the Node topology of the cluster. For example, a service can specify that traffic be preferentially routed to endpoints that are on the same Node as the client, or in the same availability zone.

It allows the Service creator to define a policy for routing traffic based upon the Node labels for the originating and destination Nodes. By using Node label matching between the source and destination, the operator may designate groups of Nodes that are “closer” and “farther” from one another, using whatever metric makes sense for that operator’s requirements.

You can control Service traffic routing by specifying the `topologyKeys` field on the Service spec. This field is a preference-order list of Node labels which will be used to sort endpoints when accessing this Service. Traffic will be directed to a Node whose value for the first label matches the originating Node’s value for that label. If there is no backend for the Service on a matching Node, then the second label will be considered, and so forth, until no labels remain.

If no match is found, the traffic will be rejected, just as if there were no backends for the Service at all.


## DNS for services and pods

Kubernetes DNS schedules a DNS Pod and Service on the cluster, and configures the kubelets to tell individual containers to use the DNS Service’s IP to resolve DNS names.

Every Service defined in the cluster (including the DNS server itself) is assigned a DNS name. By default, a client Pod’s DNS search list will include the Pod’s own namespace and the cluster’s default domain.

**Services**

* A record

  Normal (not headless) services are assigned a DNS A record for a name of the form `my-svc.my-namespace.svc.cluster-domain.example`. This resolves to the cluster IP of the Service.

  Headless (without a cluster IP) Services are also assigned a DNS A record for a name of the form `my-svc.my-namespace.svc.cluster-domain.example`. Unlike normal Services, this resolves to the set of IPs of the pods selected by the Service.

* SRV record

  For each named port, the SRV record would have the form `_my-port-name._my-port-protocol.my-svc.my-namespace.svc.cluster-domain.example`. 

  For a regular service, this resolves to the port number and the domain name: `my-svc.my-namespace.svc.cluster-domain.example`. 

  For a headless service, this resolves to multiple answers, one for each pod that is backing the service, and contains the port number and the domain name of the pod of the form `auto-generated-name.my-svc.my-namespace.svc.cluster-domain.example`

**Pods**

When a pod is created, its hostname is the Pod’s `metadata.name` value. The Pod spec has an optional `hostname` field, which can be used to specify the Pod’s hostname. The Pod spec also has an optional `subdomain` field which can be used to specify its subdomain.

DNS policies can be set on a per-pod basis:
- Default: The Pod inherits the name resolution configuration from the node that the pods run on.
- ClusterFirst: Any DNS query that does not match the configured cluster domain suffix, such as “`www.kubernetes.io`”, is forwarded to the upstream nameserver inherited from the node. Cluster administrators may have extra stub-domain and upstream DNS servers configured.
- ClusterFirstWithHostNet: For Pods running with hostNetwork, you should explicitly set its DNS policy “`ClusterFirstWithHostNet`”.
- None: It allows a Pod to ignore DNS settings from the Kubernetes environment. All DNS settings are supposed to be provided using the `dnsConfig` field in the Pod Spec.

Pod’s DNS Config allows users more control on the DNS settings for a Pod:
- `nameservers`: a list of IP addresses that will be used as DNS servers for the Pod.
- `searches`: a list of DNS search domains for hostname lookup in the Pod.
- `options`: an optional list of objects where each object may have a `name` property (required) and a `value` property (optional).

```yaml
apiVersion: v1
kind: Pod
metadata:
  namespace: default
  name: dns-example
spec:
  containers:
    - name: test
      image: nginx
  dnsPolicy: "None"
  dnsConfig:
    nameservers:
      - 1.2.3.4
    searches:
      - ns1.svc.cluster-domain.example
      - my.dns.search.suffix
    options:
      - name: ndots
        value: "2"
      - name: edns0
```


## Connecting Applications with Services

**The Kubernetes model for connecting containers**

Docker uses host-private networking, so containers can talk to other  containers only if they are on the same machine. In order for Docker  containers to communicate across nodes, there must be allocated ports on the machine’s own IP address, which are then forwarded or proxied to  the containers. 

Kubernetes assumes that pods can communicate with other pods,  regardless of which host they land on. Kubernetes gives every pod its  own cluster-private IP address, so you do not need to explicitly create  links between pods or map container ports to host ports. This means that containers within a Pod can all reach each other’s ports on localhost,  and all pods in a cluster can see each other without NAT.

Exposing pods to the cluster. This makes it accessible from any node in your cluster:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 2
  template:
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
```

**Creating a Service**

A Kubernetes Service is an abstraction which defines a logical set of  Pods running somewhere in your cluster, that all provide the same  functionality. When created, each Service is assigned a unique IP  address (also called clusterIP). This address is tied to the lifespan of the Service, and will not change while the Service is alive. Pods can  be configured to talk to the Service, and know that communication to the Service will be automatically load-balanced out to some pod that is a  member of the Service.

You can create a Service for your deployment with `kubectl expose`.

As mentioned previously, a Service is backed by a group of Pods. These Pods are exposed through `endpoints`. The Service’s selector will be evaluated continuously and the results will be POSTed to an Endpoints object. When a Pod dies, it is automatically removed from the endpoints, and new Pods matching the Service’s selector will automatically get added to the endpoints.

**Accessing the Service**

Kubernetes supports 2 primary modes of finding a Service - environment variables and DNS:

* Environment Variables: When a Pod runs on a Node, the kubelet adds a set of environment variables for each active Service. This introduces an ordering problem. Another disadvantage of doing this is that the scheduler might put both Pods on the same machine, which will take your entire Service down if it dies. 
* DNS: Kubernetes offers a DNS cluster addon Service that automatically assigns dns names to other Services.

**Exposing the Service**

You may want to expose a Service onto an external IP address. Kubernetes supports two ways of doing this: NodePorts and LoadBalancers.


## Ingress

An API object that manages external access to the services in a cluster, typically HTTP. Ingress can provide load balancing, SSL termination and name-based virtual hosting.

You must have an [ingress controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers) to satisfy an Ingress. Only creating an Ingress resource has no effect. You may need to deploy an Ingress controller such as [ingress-nginx](https://kubernetes.github.io/ingress-nginx/deploy/). You can choose from a number of [Ingress controllers](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers).

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: test-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /testpath
        backend:
          serviceName: test
          servicePort: 80
```

**Ingress rules**

Each HTTP rule contains the following information:

- An optional host. If no host is specified, the rule applies to all inbound HTTP traffic through the IP address specified. If a host is provided (for example, foo.bar.com), the rules apply to that host.
- A list of paths (for example, `/testpath`), each of which has an associated backend defined with a `serviceName` and `servicePort`. Both the host and path must match the content of an incoming request before the load balancer directs traffic to the referenced Service.
- A backend is a combination of Service and port names as described in the [Service doc](https://kubernetes.io/docs/concepts/services-networking/service/). HTTP (and HTTPS) requests to the Ingress that matches the host and path of the rule are sent to the listed backend.

A default backend is often configured in an Ingress controller to service any requests that do not match a path in the spec.

**Types of Ingress**

* Single Service Ingress - a *default backend* with no rules.

* Simple fanout - routes traffic from a single IP address to more than one Service, based on the HTTP URI being requested.
```
foo.bar.com -> 178.91.123.132 -> / foo    service1:4200
                                 / bar    service2:8080
```

* Name based virtual hosting - routing HTTP traffic to multiple host names at the same IP address.
```none
foo.bar.com --|                 |-> foo.bar.com service1:80
              | 178.91.123.132  |
bar.foo.com --|                 |-> bar.foo.com service2:80
```

* TLS - You can secure an Ingress by specifying a Secret that contains a TLS private key and certificate. Currently the Ingress only supports a single TLS port, 443, and assumes TLS termination.
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: testsecret-tls
  namespace: default
data:
  tls.crt: base64 encoded cert
  tls.key: base64 encoded key
type: kubernetes.io/tls
```

  Referencing this secret in an Ingress tells the Ingress controller to secure the channel from the client to the load balancer using TLS. You need to make sure the TLS secret you created came from a certificate that contains a Common Name (CN), also known as a Fully Qualified Domain Name (FQDN) for `sslexample.foo.com`.
```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: tls-example-ingress
spec:
  tls:
  - hosts:
    - sslexample.foo.com
    secretName: testsecret-tls
  rules:
    - host: sslexample.foo.com
      http:
        paths:
        - path: /
          backend:
            serviceName: service1
            servicePort: 80
```

* Loadbalancing - An Ingress controller is bootstrapped with some load balancing policy settings that it applies to all Ingress, such as the load balancing algorithm, backend weight scheme, and others.

Health checks are not exposed directly through the Ingress, there exist parallel concepts in Kubernetes such as [readiness probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) that allow you to achieve the same end result. 


## Ingress Controllers

In order for the Ingress resource to work, the cluster must have an ingress controller running.

Unlike other types of controllers which run as part of the `kube-controller-manager` binary, Ingress controllers are not started automatically with a cluster.

Kubernetes as a project currently supports and maintains [GCE](https://git.k8s.io/ingress-gce/README.md) and [nginx](https://git.k8s.io/ingress-nginx/README.md) controllers.

You may deploy [any number of ingress controllers](https://git.k8s.io/ingress-nginx/docs/user-guide/multiple-ingress.md#multiple-ingress-controllers) within a cluster. When you create an ingress, you should annotate each ingress with the appropriate [`ingress.class`](https://git.k8s.io/ingress-gce/docs/faq/README.md#how-do-i-run-multiple-ingress-controllers-in-the-same-cluster) to indicate which ingress controller should be used if more than one exists within your cluster.

If you do not define a class, your cloud provider may use a default ingress controller.


## Network Policies

A network policy is a specification of how groups of pods are allowed to communicate with each other and other network endpoints.

Network policies are implemented by the network plugin, so you must be using a networking solution which supports `NetworkPolicy` - simply creating the resource without a controller to implement it will have no effect.

By default, pods are non-isolated; they accept traffic from any source.

Pods become isolated by having a NetworkPolicy that selects them. Once there is any NetworkPolicy in a namespace selecting a particular pod, that  pod will reject any connections that are not allowed by any  NetworkPolicy.

Network policies do not conflict, they are additive. If any policy or  policies select a pod, the pod is restricted to what is allowed by the  union of those policies’ ingress/egress rules. Thus, order of evaluation does not affect the policy result.

See the [NetworkPolicy](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#networkpolicy-v1-networking-k8s-io) for a full definition of the resource.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - ipBlock:
        cidr: 172.17.0.0/16
        except:
        - 172.17.1.0/24
    - namespaceSelector:
        matchLabels:
          project: myproject
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 6379
  egress:
  - to:
    - ipBlock:
        cidr: 10.0.0.0/24
    ports:
    - protocol: TCP
      port: 5978
```

A `podSelector`  which selects the grouping of pods to which the policy applies. The  example policy selects pods with the label “role=db”. An empty `podSelector` selects all pods in the namespace.

A `policyTypes` list which may include either `Ingress`, `Egress`, or both. The `policyTypes` field indicates whether or not the given policy applies to ingress  traffic to selected pod, egress traffic from selected pods, or both. If  no `policyTypes` are specified on a NetworkPolicy then by default `Ingress` will always be set and `Egress` will be set if the NetworkPolicy has any egress rules.

A list of whitelist `ingress` rules. Each rule allows traffic which matches both the `from` and `ports` sections. The example policy contains a single rule, which matches  traffic on a single port, from one of three sources, the first specified via an `ipBlock`, the second via a `namespaceSelector` and the third via a `podSelector`.

A list of whitelist `egress` rules. Each rule allows traffic which matches both the `to` and `ports` sections. The example policy contains a single rule, which matches traffic on a single port to any destination in `10.0.0.0/24`.

There are four kinds of selectors that can be specified in an `ingress` `from` section or `egress` `to` section:
- **podSelector**: This selects particular Pods in the same namespace as the `NetworkPolicy` which should be allowed as ingress sources or egress destinations.
- **namespaceSelector**: This selects particular namespaces for which all Pods should be allowed as ingress sources or egress destinations.
- **namespaceSelector** *and* **podSelector**: A single `to`/`from` entry that specifies both `namespaceSelector` and `podSelector` selects particular Pods within particular namespaces.
- **ipBlock**: This selects particular IP CIDR ranges to  allow as ingress sources or egress destinations. These should be  cluster-external IPs, since Pod IPs are ephemeral and unpredictable.

### Adding entries to Pod /etc/hosts with HostAliases

Adding entries to a Pod’s /etc/hosts file provides Pod-level override of hostname resolution when DNS and other options are not applicable.  In 1.7, users can add these custom entries with the HostAliases field in PodSpec.

Modification not using HostAliases is not suggested  because the file is managed by Kubelet and can be overwritten on during  Pod creation/restart.

### IPv4/IPv6 dual-stack

IPv4/IPv6 dual-stack enables the allocation of both IPv4 and IPv6 addresses to [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) and [Services ](https://kubernetes.io/docs/concepts/services-networking/service/).

If you enable IPv4/IPv6 dual-stack networking for your  Kubernetes cluster, the cluster will support the simultaneous assignment of both IPv4 and IPv6 addresses.

Enabling IPv4/IPv6 dual-stack on your Kubernetes cluster provides the following features:
- Dual-stack Pod networking (a single IPv4 and IPv6 address assignment per Pod)
- IPv4 and IPv6 enabled Services (each Service must be for a single address family)
- Kubenet multi address family support (IPv4 and IPv6)
- Pod off-cluster egress routing (eg. the Internet) via both IPv4 and IPv6 interfaces

To enable IPv4/IPv6 dual-stack, enable the `IPv6DualStack` [feature gate](https://kubernetes.io/docs/reference/command-line-tools-reference/feature-gates/) for the relevant components of your cluster, and set dual-stack cluster network assignments.
