## Cloud Router overview

> References:
> https://cloud.google.com/network-connectivity/docs/router/concepts/overview

**Cloud Router dynamically advertises subnets and propagates learned routes in the region where the router is configured or throughout the entire Virtual Private Cloud (VPC) network.**



Cloud Router is a fully distributed and managed Google Cloud  service that programs [custom dynamic routes](https://cloud.google.com/vpc/docs/routes#dynamic_routes)  and scales with your network traffic. 

Cloud Router isn't a connectivity option, but a service that works over Cloud VPN  or Cloud Interconnect connections to provide dynamic routing by using the  [Border Gateway Protocol (BGP)](https://www.wikipedia.org/wiki/Border_Gateway_Protocol)  for your VPC networks.

> Border Gateway Protocol (BGP) is a standardized exterior gateway protocol designed to exchange routing and reachability information among autonomous systems on the Internet. BGP is classified as a path-vector routing protocol and it makes routing decisions based on paths, network policies, or rule-sets configured by a network administrator. 

Cloud Router isn't a physical device that might cause a bottleneck, and it can't be  used by itself. However, it is required or recommended in the following cases:

- Required for Cloud NAT
- Required for Cloud Interconnect and HA VPN
- A recommended configuration option for Classic VPN

When you extend your on-premises network to Google Cloud, use Cloud Router to dynamically exchange routes between your Google Cloud networks and your on-premises network. Cloud Router peers with your on-premises VPN gateway or router. The routers exchange topology information through BGP.

Topology changes automatically propagate between your VPC network and your  on-premises network. When using Cloud Router, you don't need to configure static routes.

Cloud Routers are implemented by one or two (when two interfaces connected to a Cloud VPN tunnel) software tasks.

#### Static versus dynamic routing

With static routes, you must create or maintain a routing table. A topology change on either network requires you to manually update static routes. Also, static routes can't automatically reroute traffic if a link fails.

With Cloud Router, you can use BGP to exchange routing information between networks. Instead of manually configuring static routes, networks automatically and rapidly discover topology changes through BGP. Changes are seamlessly implemented without disrupting traffic. 

#### Route advertisements

Through BGP, Cloud Router advertises the IP addresses that clients in your on-premises network can reach. Your on-premises network sends packets to your VPC network that have a destination IP address matching an advertised IP range. After reaching Google Cloud, your VPC network's firewall rules and routes determine how Google Cloud handles the packets.

You can use Cloud Router's default advertisements or explicitly specify which CIDR ranges to advertise. 

If you don't specify advertisements, Cloud Router uses the default. By default, Cloud Router advertises subnets in its region for regional dynamic routing or all subnets in a VPC network for global dynamic routing. New subnets are automatically advertised by Cloud Router. Also, if a subnet has a secondary IP range for configuring [alias IP addresses](https://cloud.google.com/vpc/docs/alias-ip), Cloud Router advertises both the primary and secondary IP addresses.

#### Learned custom dynamic routes

When a Cloud Router receives multiple next hops for the same destination prefix, Google Cloud uses route metrics and, in some cases, AS path length to create custom dynamic routes in your VPC network.

#### Route metrics

When Cloud Router advertises or propagates routes, it uses route metrics to specify route priorities. When you have multiple paths between your VPC network and on-premises network, route metrics determine a preferred path. This value is equivalent to the MED value. A lower route metric (MED) indicates higher priority.

A route metric is composed of a base advertised route priority and a regional cost. The base priority is a user-specified value, whereas the regional cost is a Google-generated value that you can't modify. The regional cost represents the cost of communicating between two regions in a VPC network. Cloud Router adds these two values together to generate a route metric.

#### Default route

When no route is specified for a particular IP destination, traffic is sent to a default route, which acts like a last resort when no other options exist. For example, Google Cloud VPC networks automatically include a default route (`0.0.0.0/0`) that sends traffic to the internet gateway.


## Best practices for Cloud Router

- Enable graceful restart on your on-premises BGP device. With graceful restart, traffic between networks won't be disrupted in the event of a Cloud Router or BGP device failure as long as the BGP session is re-established within the graceful restart period.
- If graceful restart is not supported or enabled on your device, you should configure two on-premises devices with one tunnel each to provide redundancy.
- For high reliability, set up redundant routers and BGP sessions even if your on-premises device supports graceful restart. In the event of non-transient failures, you'll be protected even if one path fails.
- To connect your on-premises network to multiple Google Cloud projects by using dynamic routing, see these scenarios for [VPC Network Peering](https://cloud.google.com/vpc/docs/vpc-peering#transit-network) or [Shared VPC](https://cloud.google.com/vpc/docs/shared-vpc#hybrid_cloud_scenario).


## Commands

```bash
# Viewing a VPC network's dynamic routing mode
gcloud compute networks describe [NETWORK_NAME]

# Creating Cloud Routers
gcloud compute routers create my-router \
    --network [NETWORK] \
    --asn [ASN_NUMBER] \
    --advertisement-mode custom \
    --set-advertisement-groups all_subnets \
    --set-advertisement-ranges 1.2.3.4,6.7.0.0/16
```
