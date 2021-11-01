# Admission Controllers

> References:
>
> https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#mutatingadmissionwebhook
>
> https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/



An admission controller is a piece of code that intercepts requests to the Kubernetes API server prior to persistence of the object, but after the request is authenticated and authorized.  

MutatingAdmissionWebhook and ValidatingAdmissionWebhook - execute the mutating and validating (respectively) admission control webhooks which are configured in the API.

Admission controllers may be "validating", "mutating", or both.

In the first phase, mutating admission controllers are run. In the second phase, validating admission controllers are run.

Turn on/off:

```
kube-apiserver --enable-admission-plugins=NamespaceLifecycle,LimitRanger ...
kube-apiserver --disable-admission-plugins=PodNodeSelector,AlwaysDeny ...
```

#### MutatingAdmissionWebhook

This admission controller calls any mutating webhooks which match the request. Matching webhooks are called in serial; each one may modify the object if it desires.

#### ValidatingAdmissionWebhook

This admission controller calls any validating webhooks which match the request. Matching webhooks are called in parallel; if any of them rejects the request, the request fails.

# Dynamic Admission Control

In addition to compiled-in admission plugins, admission plugins can be developed as extensions and run as webhooks configured at runtime.

You can dynamically configure what resources are subject to what admission webhooks via ValidatingWebhookConfiguration or MutatingWebhookConfiguration.

If your admission webhooks require authentication, you can configure the apiservers to use basic auth, bearer token, or a cert to authenticate itself to the webhooks.

Webhooks are sent as POST requests, with `Content-Type: application/json`, with an `AdmissionReview` API object in the `admission.k8s.io` API group serialized to JSON as the body.

Webhooks respond with a 200 HTTP status code, `Content-Type: application/json`, and a body containing an `AdmissionReview` object (in the same version they were sent), with the `response` stanza populated, serialized to JSON.

To register admission webhooks, create `MutatingWebhookConfiguration` or `ValidatingWebhookConfiguration` API objects. The name of a `MutatingWebhookConfiguration` or a `ValidatingWebhookConfiguration` object must be a valid [DNS subdomain name](https://kubernetes.io/docs/concepts/overview/working-with-objects/names#dns-subdomain-names).

Each webhook must specify a list of rules used to determine if a request to the API server should be sent to the webhook.

