## Kubernetes Authorization

> References:
>
> - [Access Control](../../security/access-control.md)


The Kubernetes API server may authorize a request using one of several authorization modes:

- **Node** - A special-purpose authorization mode that grants permissions to kubelets based on the pods they are scheduled to run. See [Node Authorization](https://kubernetes.io/docs/reference/access-authn-authz/node/).

- **ABAC** - Attribute-based access control (ABAC) defines an access control  paradigm whereby access rights are granted to users through the use of policies which combine attributes together. The policies can use any  type of attributes (user attributes, resource attributes, object, environment attributes, etc). See [ABAC Mode](https://kubernetes.io/docs/reference/access-authn-authz/abac/).

  - A request has attributes which correspond to the properties of a policy object.

  - When a request is received, the attributes are determined. Unknown attributes are set to the zero value of its type (e.g. empty string, 0, false).

  - A property set to `*` will match any value of the corresponding attribute.

  - The tuple of attributes is checked for a match against every policy in the policy file. If at least one line matches the request attributes, then the request is authorized (but may fail later validation).

  - To permit any authenticated user to do something, write a policy with the group property set to `system:authenticated`.

  - To permit any unauthenticated user to do something, write a policy with the group property set to `system:unauthenticated`.

  - To permit a user to do anything, write a policy with the apiGroup, namespace, resource, and nonResourcePath properties set to `*`.

  - For example, kubelet policy that can read and write events:

  ```json
  {
    "apiVersion": "abac.authorization.kubernetes.io/v1beta1",
    "kind": "Policy",
    "spec": {
      "user": "kubelet",
      "namespace": "*",
      "resource": "events"
    }
  }
  ```

- **RBAC** - Role-based access control (RBAC) is a method of regulating access to computer or network resources based on the roles of individual users within an enterprise. In this context, access is the ability of an individual user to perform a specific task, such as view, create, or modify a file. See [RBAC Mode](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

  - When specified RBAC (Role-Based Access Control) uses the `rbac.authorization.k8s.io` API group to drive authorization decisions, allowing admins to dynamically configure permission policies through the Kubernetes API.
  - To enable RBAC, start the apiserver with `--authorization-mode=RBAC`.
  - The RBAC API declares four kinds of Kubernetes object: *Role*, *ClusterRole*, *RoleBinding* and *ClusterRoleBinding*. You can [describe objects](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/#understanding-kubernetes-objects), or amend them, using tools such as `kubectl,` just like any other Kubernetes object.

- **Webhook** - A WebHook is an HTTP callback: an HTTP POST that occurs when something happens; a simple event-notification via HTTP POST. A web application implementing WebHooks will POST a message to a URL when certain things happen. See [Webhook Mode](https://kubernetes.io/docs/reference/access-authn-authz/webhook/).