## kiam/kube2iam

> References:
> https://github.com/jtblin/kube2iam
> https://github.com/uswitch/kiam
> https://pingles.medium.com/kiam-iterating-for-security-and-reliability-5e793ab93ec3


```
#aws #iam #kubernetes #kiam #kube2iam
```

### kiam

[Kiam](https://github.com/uswitch/kiam) bridges Kubernetes' Pods with Amazon's [Identity and Access Management](https://aws.amazon.com/iam/) (IAM). 

kiam runs as an agent on each node in your Kubernetes cluster and allows cluster users to associate IAM roles to Pods.

Kiam intercepts Metadata API requests.

Separated Agent and Server processes. Allows user workloads to run on nodes without `sts:AssumeRole` permissions to enhance cluster security.

Pods can assume roles from any AWS account assuming trust relationships permit it.

kiam uses an annotation added to a `Pod` to indicate which role should be assumed. For example:

```yaml
kind: Pod
metadata:
  name: foo
  namespace: iam-example
  annotations:
    iam.amazonaws.com/role: reportingdb-reader
```

Agent

The agent runs an HTTP proxy which intercepts credentials requests and  passes on anything else. An DNAT iptables [rule](https://github.com/uswitch/kiam/blob/master/cmd/kiam/iptables.go) is required to intercept the traffic. The agent is capable of adding and removing the required rule for you through use of the `--iptables` [flag](https://github.com/uswitch/kiam/blob/master/cmd/kiam/agent.go). This is the name of the interface where pod traffic originates and it  is different for the various CNI implementations. The flag also supports the `!` prefix for inverted matches should you need to match all but one interface.

Server

This process is responsible for connecting to the Kubernetes API  Servers to watch Pods and communicating with AWS STS to request  credentials. It also maintains a cache of credentials for roles  currently in use by running pods- ensuring that credentials are  refreshed every few minutes and stored in advance of Pods needing them.

### kube2iam

IAM roles are attributed through instance profiles and are accessible by services through the transparent usage by the aws-sdk of the ec2 metadata API. When using the aws-sdk, a call is made to the EC2 metadata API which provides temporary credentials that are then used to make calls to the AWS service.

The problem is that in a multi-tenanted containers based world, multiple containers will be sharing the underlying nodes, this would mean that one needs to create an IAM role which is a union of all IAM roles.

kube2iam redirects the traffic that is going to the ec2 metadata API for docker containers to a container running on each instance, make a call to the AWS API to retrieve temporary credentials and return these to the caller. Other calls will be proxied to the EC2 metadata API. This container will need to run with host networking enabled so that it can call the EC2 metadata API itself.

Usage:

- IAM role `sts:AssumeRole`
- kube2iam daemonset
- iptables
- kubernetes annotation ` iam.amazonaws.com/role: role-arn`
- Namespace Restrictions
- RBAC Setup