## Kustomize - Kubernetes native configuration management

> References:
> https://github.com/kubernetes-sigs/kustomize

`kustomize` lets you customize raw, template-free YAML files for multiple purposes, leaving the original YAML untouched and usable as is.

`kustomize` targets kubernetes; it understands and can patch [kubernetes style](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#kubernetes-style-object) API objects.  It's like [`make`](https://www.gnu.org/software/make), in that what it does is declared in a file, and it's like [`sed`](https://www.gnu.org/software/sed), in that it emits edited text.

## Usage

1. Make a [kustomization](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#kustomization) file.

   In some directory containing your YAML [resource](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#resource) files (deployments, services, configmaps, etc.), create a [kustomization](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#kustomization) file.

   This file should declare those resources, and any customization to apply to them, e.g. *add a common label*.
   
   File structure:
   ```
   ~/someApp
   ├── deployment.yaml
   ├── kustomization.yaml
   └── service.yaml
   ```
   Generate customized YAML with:
   ```
   kustomize build ~/someApp
   ```
   The YAML can be directly [applied](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#apply) to a cluster:
   ```
   kustomize build ~/someApp | kubectl apply -f -
   ```

2. Create [variants](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#variant) using [overlays](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#overlay)

   Manage traditional [variants](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#variant) of a configuration - like *development*, *staging* and *production* - using [overlays](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#overlay) that modify a common [base](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#base).

   File structure:

   ```
   ~/someApp
   ├── base
   │   ├── deployment.yaml
   │   ├── kustomization.yaml
   │   └── service.yaml
   └── overlays
       ├── development
       │   ├── cpu_count.yaml
       │   ├── kustomization.yaml
       │   └── replica_count.yaml
       └── production
           ├── cpu_count.yaml
           ├── kustomization.yaml
           └── replica_count.yaml
   ```

## Installation

```bash
brew install kustomize
```

## Glossary

#### application

An *application* is a group of k8s resources related by some common purpose, e.g.  a load balancer in front of a webserver backed by a database. [Resource](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#resource) labelling, naming and metadata schemes have historically served to group resources together for collective operations like *list* and *remove*.

#### apply

The verb *apply* in the context of k8s refers to a kubectl command and an in-progress [API endpoint](https://goo.gl/UbCRuf) for mutating a cluster.

One *applies* a statement of what one wants to a cluster in the form of a complete resource list.

The cluster merges this with the previously applied state and the actual state to arrive at a new desired state, which the cluster's reconciliation loop attempts to create.  This is the foundation of level-based state management in k8s.

####  base

A *base* is a [kustomization](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#kustomization) referred to by some other [kustomization](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#kustomization).

Any kustomization, including an [overlay](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#overlay), can be a base to another kustomization.

A base has no knowledge of the overlays that refer to it.

#### bespoke configuration

A *bespoke* configuration is a [kustomization](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#kustomization) and some [resources](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#resource) created and maintained internally by some organization for their own purposes.

The [workflow](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/workflows.md) associated with a *bespoke* config is simpler than the workflow associated with an [off-the-shelf](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#off-the-shelf-configuration) config, because there's no notion of periodically capturing someone else's upgrades to the [off-the-shelf](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#off-the-shelf-configuration) config.

![bespoke config workflow image](.KUSTOMIZE-images/workflowBespoke.jpg)

#### custom resource definition

One can extend the k8s API by making a Custom Resource Definition ([CRD spec](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/)).

This defines a custom [resource](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#resource) (CD), an entirely new resource that can be used alongside *native* resources like ConfigMaps, Deployments, etc.

Kustomize can customize a CD, but to do so kustomize must also be given the corresponding CRD so that it can interpret the structure correctly.

#### declarative application management

Kustomize aspires to support [Declarative Application Management](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/architecture/declarative-application-management.md), a set of best practices around managing k8s clusters.

In brief, kustomize should

- Work with any configuration, be it bespoke, off-the-shelf, stateless, stateful, etc.
- Support common customizations, and creation of [variants](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#variant) (e.g. *development* vs. *staging* vs. *production*).
- Expose and teach native k8s APIs, rather than hide them.
- Add no friction to version control integration to support reviews and audit trails.
- Compose with other tools in a unix sense.
- Eschew crossing the line into templating, domain specific languages, etc., frustrating the other goals.

#### kustomization

The term *kustomization* refers to a `kustomization.yaml` file.

A kustomization file contains [fields](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/fields.md) falling into four categories:

- *resources* - what existing [resources](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#resource) are to be customized. Example fields: *resources*, *crds*.
- *generators* - what *new* resources should be created. Example fields: *configMapGenerator* (legacy), *secretGenerator* (legacy), *generators* (v2.1).
- *transformers* - what to *do* to the aforementioned resources. Example fields: *namePrefix*, *nameSuffix*, *images*, *commonLabels*, *patchesJson6902*, etc. and the more general *transformers* (v2.1) field.
- *meta* - fields which may influence all or some of the above.  Example fields: *vars*, *namespace*, *apiVersion*, *kind*, etc.

#### kustomization root

The directory that immediately contains a `kustomization.yaml` file.

Data files like resource YAML files, or text files containing *name=value* pairs intended for a ConfigMap or Secret, or files representing a patch to be used in a patch transformation, must live *within or below* the root, and as such are specified via *relative paths* exclusively.

Other kustomizations (other directories containing a `kustomization.yaml` file) may be referred to by URL, by absolute path, or by relative path.

A common layout is:

```
├── base
│   ├── deployment.yaml
│   ├── kustomization.yaml
│   └── service.yaml
└── overlays
    ├── dev
    │   ├── kustomization.yaml
    │   └── patch.yaml
    ├── prod
    │   ├── kustomization.yaml
    │   └── patch.yaml
    └── staging
        ├── kustomization.yaml
        └── patch.yaml
```

The three roots `dev`, `prod` and `staging` (presumably) all refer to the `base` root.  One would have to inspect the `kustomization.yaml` files to be sure.

#### off-the-shelf configuration

An *off-the-shelf* configuration is a kustomization and resources intentionally published somewhere for others to use.

```
github.com/username/someapp/
  kustomization.yaml
  deployment.yaml
  configmap.yaml
  README.md
```

Someone could then *fork* this repo (on github) and *clone* their fork to their local disk for customization.

This clone could act as a [base](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#base) for the user's own [overlays](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#overlay) to do further customization.

![off-the-shelf config workflow image](.KUSTOMIZE-images/workflowOts.jpg)

#### overlay

An *overlay* is a kustomization that depends on another kustomization.

The [kustomizations](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#kustomization) an overlay refers to (via file path, URI or other method) are called [bases](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#base).

An overlay is unusable without its bases.

An overlay may act as a base to another overlay.

#### patch

General instructions to modify a resource.

There are two alternative techniques with similar power but different notation - the [strategic merge patch](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#patchstrategicmerge) and the [JSON patch](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#patchjson6902).

#### patchStrategicMerge

A *patchStrategicMerge* is [strategic-merge](https://git.k8s.io/community/contributors/devel/sig-api-machinery/strategic-merge-patch.md)-style patch (SMP).

An SMP looks like an incomplete YAML specification of a k8s resource.  The SMP includes `TypeMeta` fields to establish the group/version/kind/name of the [resource](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#resource) to patch, then just enough remaining fields to step into a nested structure to specify a new field value, e.g. an image tag.

By default, an SMP *replaces* values.  This usually desired when the target value is a simple string, but may not be desired when the target value is a list. To change this default behavior, add a *directive*.  Recognized directives in YAML patches are *replace* (the default) and *delete* (see [these notes](https://git.k8s.io/community/contributors/devel/sig-api-machinery/strategic-merge-patch.md)).

Note that for custom resources, SMPs are treated as [json merge patches](https://tools.ietf.org/html/rfc7386).

#### patchJson6902

A *patchJson6902* refers to a kubernetes [resource](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#resource) and a [JSONPatch](https://tools.ietf.org/html/rfc6902) specifying how to change the resource.

A *patchJson6902* can do almost everything a *patchStrategicMerge* can do, but with a briefer syntax.

#### plugin

A chunk of code used by kustomize, but not necessarily compiled into kustomize, to generate and/or transform a kubernetes resource as part of a kustomization.

#### target

The *target* is the argument to `kustomize build`, e.g.:
```
kustomize build $target
```

`$target` must be a path or a url to a [kustomization](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#kustomization).

The target contains, or refers to, all the information needed to create customized resources to send to the [apply](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#apply) operation.

A target can be a [base](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#base) or an [overlay](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#overlay).

#### transformer

A transformer can modify a resource, or merely visit it and collect information about it in the course of a `kustomize build`.

#### variant

A *variant* is the outcome, in a cluster, of applying an [overlay](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#overlay) to a [base](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#base).

E.g., a *staging* and *production* overlay both modify some common base to create distinct variants.