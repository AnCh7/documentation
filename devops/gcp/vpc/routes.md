# Routes

> References:
> https://cloud.google.com/vpc/docs/routes
> https://cloud.google.com/vpc/docs/using-routes



Google Cloud routes define the paths that network traffic takes from a virtual machine (VM) instance to other destinations. These destinations can be inside your Google Cloud Virtual Private Cloud (VPC) network (for example, in another VM) or outside it.

In a VPC network, a route consists of a **single destination (CIDR)** and a **single next hop**. When an instance in a VPC network sends a packet, Google Cloud delivers the packet to the route's next hop if the packet's destination address is within the route's destination range.

Every VPC network uses a scalable, distributed virtual routing mechanism. There is no physical device that's assigned to the network. Some routes can be applied selectively, but the [routing table](https://wikipedia.org/wiki/Routing_table) for a VPC network is defined at the VPC network level.

Each VM instance has a controller that is kept informed of all [applicable routes](https://cloud.google.com/vpc/docs/routes#applicable_routes) from the network's routing table. Each packet leaving a VM is delivered to the appropriate next hop of an applicable route based on a routing order. When you add or delete a route, the set of changes is propagated to the VM controllers [by using an eventually consistent design](https://cloud.google.com/vpc/docs/using-routes#order_of_operations).

#### Route types

**System-generated routes:**

System-generated routes apply to all instances in a VPC network.

| **Type**                                                     | **Destination** | **Next hop**               | **Removable** |
| ------------------------------------------------------------ | --------------- | -------------------------- | ------------- |
| [default route](https://cloud.google.com/vpc/docs/routes#routingpacketsinternet) | `0.0.0.0/0`     | `default-internet-gateway` | Yes           |

- Defines the path out of the VPC network, including the path to the internet.
- Provides the standard path for [Private Google Access](https://cloud.google.com/vpc/docs/private-access-options).

| **Type**                                                     | **Destination**                                              | **Next hop**                                               | **Removable**                                                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------------------------------------------------- | ------------------------------------------------------------ |
| [subnet route](https://cloud.google.com/vpc/docs/routes#subnet-routes) | Primary and secondary      [subnet IP ranges](https://cloud.google.com/vpc/docs/vpc#vpc_networks_and_subnets) | VPC network, which forwards packets to VMs in its  subnets | Only when the subnet is deleted or when you change the subnet's secondary IP ranges |

- Define paths to each subnet in the VPC network.
- When a subnet is created, a corresponding subnet route for the subnet's primary IP range is also created.
- You cannot delete a subnet route unless you modify or delete the subnet.
- When networks are connected by using [VPC Network Peering](https://cloud.google.com/vpc/docs/vpc-peering), some subnet routes from one network are imported into the other network, and vice versa.

**Custom routes:**

Destinations for custom routes cannot match or be more specific than any subnet route in the network.

Custom static routes apply to all instances or specific instances.

| **Type**                                                     | **Destination**                                              | **Next hop**                                                 | **Removable** | **Applies to**                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------- | ------------------------------------------------------------ |
| [static route](https://cloud.google.com/vpc/docs/routes#static_routes) | **1)** IP range that does not partially or exactly overlap with any subnet IP range **2)** IP range broader than a subnet IP range | One of: **1)** Instance by name **2)** Instance by IP address  **3)** Cloud VPN tunnel | Yes           | Either:  **1)** All instances in the network **2)** Specific instances in the network, identified by network tag, if the route can be scoped by network tag |

Dynamic routes apply to instances based on the [dynamic routing mode of the VPC network](https://cloud.google.com/vpc/docs/vpc#routing_for_hybrid_networks).

| **Type**                                                     | **Destination**                                              | **Next hop**                              | **Removable**                                                | **Applies to**                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [dynamic route](https://cloud.google.com/vpc/docs/routes#dynamic_routes) | **1)** IP range that does not partially or exactly overlap with any subnet IP range **2)** IP range broader than a subnet IP range | IP address of the Cloud Router's BGP peer | Only by a Cloud Router if it no longer receives the route    from its BGP peer | **1)** Instances in the same region as the Cloud Router if the VPC network is in regional dynamic routing mode **2)** All instances if the VPC network is in global dynamic routing mode |

#### Routing order

When an instance sends a packet, Google Cloud attempts to select one route from the set of applicable routes according to the following routing order.

1. **Subnet routes are considered first** because Google Cloud requires that subnet routes have the most specific destinations matching the IP address ranges of their respective subnets. You cannot override a subnet route with any other type of route.

2. **If the packet does not fit in the destination for a subnet route**, Google Cloud looks for a custom route with the most specific destination.

3. **If more than one custom route has the same most specific destination**, Google Cloud uses the process to select a route from route candidates. Route candidates are custom routes (static or dynamic routes) that have the same most specific destination.

4. **If no applicable destination is found,** Google Cloud drops the packet, replying with an ICMP destination or network unreachable error.

#### Special return paths

VPC networks have special routes for certain services. These routes are defined outside of your VPC network, in Google's production network. They don't appear in your VPC network's routing table. You cannot remove or override them, even if you delete or replace a default route in your VPC network. However, you can control traffic to and from these services by using firewall rules:
- Return paths for HTTP(S), SSL proxy, and TCP proxy load balancers
- Return paths for network load balancers
- Health checks for all load balancer types
- IAP
- Cloud DNS

#### Static route parameters

- **Name** and **Description.**
- **Network.** Each route must be associated with exactly one VPC network.
- **Destination range.** The destination range is a single IPv4 CIDR block that contains the IP addresses of systems that receive incoming packets. Destinations must be expressed in [CIDR notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing), and the broadest destination possible is `0.0.0.0/0`.
- **Priority.** If multiple routes have identical destinations, priority is used to determine which route should be used. Lower numbers indicate higher priorities.
- **Next hop.** Static routes can have next hops that point to the default internet gateway, a Google Cloud instance, or a Cloud VPN tunnel.
- **Tags.** You can specify a list of [network tags](https://cloud.google.com/vpc/docs/add-remove-network-tags) so that the route only applies to instances that have at least one of the listed tags. If you don't specify tags, Google Cloud applies the route to all instances in the network.

#### Static route next hops

- **Next Hop Gateway (`next-hop-gateway`).** You can specify a default internet gateway to define a path to external IP addresses.
- **Next Hop Instance (`next-hop-instance`).** You can direct traffic to an existing instance in Google Cloud by specifying its name and zone. Traffic is directed to the primary internal IP address of the VM's network interface in the VPC network where you define the route.
- **Next Hop Internal TCP/UDP Load Balancer (`next-hop-ilb`).** For Internal TCP/UDP Load Balancing, you can use a load balancer's IP address as a next hop that distributes traffic among healthy backend instances.
- **Next Hop IP (`next-hop-address`).** You can provide an internal IP address assigned to a Google Cloud VM as a next hop.
- **Next Hop VPN Tunnel (`next-hop-vpn-tunnel`).** For Cloud VPN tunnels that use [policy-based routing and route-based VPNs](https://cloud.google.com/network-connectivity/docs/vpn/concepts/choosing-networks-routing#ts-tun-routing), you can direct traffic to the VPN tunnel by creating routes whose next hops refer to the tunnel by its name and region. Google Cloud ignores routes whose next hops are Cloud VPN tunnels that are down.

#### Commands

```bash
gcloud compute routes list --filter="network=NETWORK_NAME"

gcloud compute routers get-status CLOUD_ROUTER_NAME \
    --region=REGION \
    --format="flattened(result.bestRoutes)"

gcloud compute routes describe ROUTE_NAME --format="flattened()"
```
