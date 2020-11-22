## Overview of Cloud Native Security

The 4C's of Cloud Native Security

![img](.009-security-images/4c.png)

1) The Cloud (or co-located servers, or the corporate datacenter) is the [trusted computing base](https://en.wikipedia.org/wiki/Trusted_computing_base) of a Kubernetes cluster. If these components themselves are vulnerable (or configured in a vulnerable way) then there’s no real way to guarantee the security of any components built on top of this base.

| Area of Concern for Kubernetes Infrastructure | Recommendation                                               |
| --------------------------------------------- | ------------------------------------------------------------ |
| Network access to API Server (Masters)        | Ideally all access to the Kubernetes Masters is not allowed publicly on the  internet and is controlled by network access control lists restricted to the set of IP addresses needed to administer the cluster. |
| Network access to Nodes (Worker Servers)      | Nodes should be configured to *only* accept connections (via network access control lists) from the masters  on the specified ports, and accept connections for services in  Kubernetes of type NodePort and LoadBalancer. If possible, these nodes  should not be exposed on the public internet entirely. |
| Kubernetes access to Cloud Provider API       | Each cloud provider will need to grant a different set of permissions to the Kubernetes Masters and Nodes, so this recommendation will be more  generic. It is best to provide the cluster with cloud provider access  that follows the [principle of least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege) for the resources it needs to administer. An example for Kops in AWS can be found here: https://github.com/kubernetes/kops/blob/master/docs/iam_roles.md#iam-roles |
| Access to etcd                                | Access to etcd (the datastore of Kubernetes) should be limited to the masters  only. Depending on your configuration, you should also attempt to use  etcd over TLS. More info can be found here: https://github.com/etcd-io/etcd/tree/master/Documentation#security |
| etcd Encryption                               | Wherever possible it’s a good practice to encrypt all drives at rest, but since  etcd holds the state of the entire cluster (including Secrets) its disk  should especially be encrypted at rest. |

2) Cluster

There are two areas of concern for securing Kubernetes:
- Securing the components that are configurable which make up the cluster. See [securing your cluster](https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/).
- Securing the components which run in the cluster:

  | Area of Concern for Workload Security                        | Recommendation                                               |
  | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | RBAC Authorization (Access to the Kubernetes API)            | https://kubernetes.io/docs/reference/access-authn-authz/rbac/ |
  | Authentication                                               | https://kubernetes.io/docs/reference/access-authn-authz/controlling-access/ |
  | Application secrets management (and encrypting them in etcd at rest) | https://kubernetes.io/docs/concepts/configuration/secret/ https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/ |
  | Pod Security Policies                                        | https://kubernetes.io/docs/concepts/policy/pod-security-policy/ |
  | Quality of Service (and Cluster resource management)         | https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/ |
  | Network Policies                                             | https://kubernetes.io/docs/concepts/services-networking/network-policies/ |
  | TLS For Kubernetes Ingress                                   | https://kubernetes.io/docs/concepts/services-networking/ingress/#tls |

3) Container

| Area of Concern for Containers                              | Recommendation                                               |
| ----------------------------------------------------------- | ------------------------------------------------------------ |
| Container Vulnerability Scanning and OS Dependency Security | As part of an image build step or on a regular basis you should scan your  containers for known vulnerabilities with a tool such as [CoreOS’s Clair](https://github.com/coreos/clair/) |
| Image Signing and Enforcement                               | Two other CNCF Projects (TUF and Notary) are useful tools for signing  container images and maintaining a system of trust for the content of  your containers. If you use Docker, it is built in to the Docker Engine  as [Docker Content Trust](https://docs.docker.com/engine/security/trust/content_trust/). On the enforcement piece, [IBM’s Portieris](https://github.com/IBM/portieris) project is a tool that runs as a Kubernetes Dynamic Admission  Controller to ensure that images are properly signed via Notary before  being admitted to the Cluster. |
| Disallow privileged users                                   | When constructing containers, consult your documentation for how to create  users inside of the containers that have the least level of operating  system privilege necessary in order to carry out the goal of the  container. |

4) Code

| Area of Concern for Code              | Recommendation                                               |
| ------------------------------------- | ------------------------------------------------------------ |
| Access over TLS only                  | If your code needs to communicate via TCP, ideally it would be performing a TLS handshake with the client ahead of time. With the exception of a  few cases, the default behavior should be to encrypt everything in  transit. Going one step further, even “behind the firewall” in our VPC’s it’s still a good idea to encrypt network traffic between services.  This can be done through a process known as mutual or [mTLS](https://en.wikipedia.org/wiki/Mutual_authentication) which performs a two sided verification of communication between two  certificate holding services. There are numerous tools that can be used  to accomplish this in Kubernetes such as [Linkerd](https://linkerd.io/) and [Istio](https://istio.io/). |
| Limiting port ranges of communication | This recommendation may be a bit self-explanatory, but wherever possible you should only expose the ports on your service that are absolutely  essential for communication or metric gathering. |
| 3rd Party Dependency Security         | Since our applications tend to have dependencies outside of our own  codebases, it is a good practice to ensure that a regular scan of the  code’s dependencies are still secure with no CVE’s currently filed  against them. Each language has a tool for performing this check  automatically. |
| Static Code Analysis                  | Most  languages provide a way for a snippet of code to be analyzed for any  potentially unsafe coding practices. Whenever possible you should  perform checks using automated tooling that can scan codebases for  common security errors. Some of the tools can be found here: https://www.owasp.org/index.php/Source_Code_Analysis_Tools |
| Dynamic probing attacks               | There are a few automated tools that are able to be run against your service  to try some of the well known attacks that commonly befall services.  These include SQL injection, CSRF, and XSS. One of the most popular  dynamic analysis tools is the OWASP Zed Attack proxy https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project |