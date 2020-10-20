# Network tags

> References:
> https://cloud.google.com/vpc/docs/add-remove-network-tags



A tag is simply a character ==string added to a tags field in a resource,== such as [Compute Engine ](https://cloud.google.com/compute/docs) virtual machine (VM) instances or [instance templates](https://cloud.google.com/compute/docs/instance-templates). A tag is not a separate resource, so you cannot create it separately. All resources with that string are considered to have that tag. Tags enable you to make [firewall rules](https://cloud.google.com/vpc/docs/firewalls) and [routes](https://cloud.google.com/vpc/docs/routes) applicable to specific VM instances.

You can assign network tags to new VMs at creation time, or you can edit the set of assigned tags at any time later. You can edit network tags without stopping a VM.

The network tags that you assign to an instance apply to all of the instance's network interfaces. A network tag only applies to the [VPC networks](https://cloud.google.com/vpc/docs/vpc) that are directly attached to the instance's network interfaces.

#### Targets for firewall rules

Every firewall rule in Google Cloud must have a [target](https://cloud.google.com/vpc/docs/firewalls#rule_assignment) which defines the instances to which it applies. The default target is all instances in the network, but you can specify instances as targets using either target tags or target service accounts.

The target tag defines the Google Cloud VMs to which the rule applies. The rule is applied to a specific VPC network. It is made applicable to the primary internal IP address associated with the network interface of any instance attached to that VPC network that has a matching network tag.

Both ingress and egress firewall rules have targets:
- Ingress rules apply to traffic entering your VPC network. For ingress rules, the targets are destination VMs in Google Cloud.
- Egress rules apply to traffic leaving your VPC network. For egress rules, the targets are source  VMs in Google Cloud.

#### Source filters for ingress firewall rules

When you create ingress firewall rules, you must specify a [source](https://cloud.google.com/vpc/docs/firewalls#sources_or_destinations_for_the_rule). You can define it using ranges of either internal or external IP addresses or by referring to specific instances. You specify instances using either source tags or source service accounts.

The source tag for an ingress firewall rule applied on a VPC network defines a source of traffic as coming from the primary internal IP address associated with the network interface attached to that VPC network for any instance having a matching network tag.

When you use an ingress firewall rule with source tags, you might observe a propagation delay.
