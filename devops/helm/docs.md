# Helm

Helm is the package manager for Kubernetes: https://helm.sh/docs

Helm charts are a bunch of yaml files that specify how a kubernetes cluster should behave - what images the pods should use, and what services need to be exposed etc.

## Installing Helm

There are two parts to Helm: The Helm client (`helm`) and the Helm server (Tiller). 

For MacOs Helm installation use ```brew install kubernetes-helm```.

The easiest way to install `tiller`into the cluster is simply to run`helm init`. This will validate that `helm`’s local environment is set up correctly (and set it up if necessary). Then it will connect to whatever cluster `kubectl`connects to by default (`kubectl config view`). Once it connects, it will install `tiller`into the`kube-system`namespace.

Once Tiller is installed, running `helm version`should show you both the client and server version. 

Google’s GKE hosted Kubernetes platform enables RBAC by default. You will need to create a service account for tiller, and use the –service-account flag when initializing the helm server.

## Glossary

#### Chart 

A Chart is a Helm package. It contains all of the resource definitions necessary to run an application, tool, or service inside of a Kubernetes cluster. Think of it like the Kubernetes equivalent of a Homebrew formula, an Apt dpkg, or a Yum RPM file.

Charts contain a `Chart.yaml`file as well as templates, default values (`values.yaml`), and dependencies, for example:

```
wordpress/
  Chart.yaml          # A YAML file containing information about the chart
  LICENSE             # OPTIONAL: A plain text file containing the license for the chart
  README.md           # OPTIONAL: A human-readable README file
  requirements.yaml   # OPTIONAL: A YAML file listing dependencies for the chart
  values.yaml         # The default configuration values for this chart
  charts/             # A directory containing any charts upon which this chart depends.
  templates/          # A directory of templates that, when combined with values, 
                      # will generate valid Kubernetes manifest files.
  templates/NOTES.txt # OPTIONAL: A plain text file containing short usage notes
```

The `Chart.yaml` file is required for a chart. It contains the following fields:

```yaml
apiVersion: The chart API version, always "v1" (required)
name: The name of the chart (required)
version: A SemVer 2 version (required)
kubeVersion: A SemVer range of compatible Kubernetes versions (optional)
description: A single-sentence description of this project (optional)
keywords:
  - A list of keywords about this project (optional)
home: The URL of this project's home page (optional)
sources:
  - A list of URLs to source code for this project (optional)
maintainers: # (optional)
  - name: The maintainer's name (required for each maintainer)
    email: The maintainer's email (optional for each maintainer)
    url: A URL for the maintainer (optional for each maintainer)
engine: gotpl # The name of the template engine (optional, defaults to gotpl)
icon: A URL to an SVG or PNG image to be used as an icon (optional).
appVersion: The version of the app that this contains (optional). This needn't be SemVer.
deprecated: Whether this chart is deprecated (optional, boolean)
tillerVersion: The version of Tiller that this chart requires. This should be expressed as a SemVer range: ">2.0.0" (optional)
```

In Helm, one chart may depend on any number of other charts. These dependencies can be dynamically linked through the `requirements.yaml` file or brought in to the `charts/` directory and managed manually, for example:

```yaml
dependencies:
  - name: apache
    version: 1.2.3
    repository: http://example.com/charts
  - name: mysql
    version: 3.2.1
    repository: http://another.example.com/charts
```

Once you have a dependencies file, you can run `helm dependency update` and it will use your dependency file to download all the specified charts into your `charts/` directory for you.

In addition to the other fields above, each requirements entry may contain the optional fields `tags` and `condition`.

#### Chart Archive

A chart archive is a tarred and gzipped (and optionally signed) chart.

#### Repository

A Repository is the place where charts can be collected and shared. It’s like Perl’s [CPAN archive](https://www.cpan.org/) or the [Fedora Package Database](https://apps.fedoraproject.org/packages/s/pkgdb), but for Kubernetes packages. 

A chart repository consists of packaged charts and a special file called `index.yaml` which contains an index of all of the charts in the repository. It contains some metadata about the package, including the contents of a chart’s `Chart.yaml` file. 

An example of chart repo:

```txt
charts/
  |- index.yaml
  |- alpine-0.1.2.tgz
  |- alpine-0.1.2.tgz.prov
```

#### Release

A Release is an instance of a chart running in a Kubernetes cluster. One chart can often be installed many times into the same cluster. And each time it is installed, a new release is created. Consider a MySQL chart. If you want two databases running in your cluster, you can install that chart twice. Each one will have its own release, which will in turn have its own release name. Release can also be rolled back to a previous release number. This is done with the `helm rollback`command.

#### Plugins

A plugin is a tool that can be accessed through the `helm` CLI, but which is not part of the built-in Helm codebase. Helm plugins live in `$(helm home)/plugins`.

#### Provenance (Provenance file)

Helm charts may be accompanied by a provenance file which provides information about where the chart came from and what it contains. A provenance contains a cryptographic hash of the chart archive file, the Chart.yaml data, and a signature block (an OpenPGP “clearsign” block). 

If a chart is named `myapp-1.2.3.tgz`, its provenance file will be `myapp-1.2.3.tgz.prov`. Provenance files are generated at packaging time (`helm package --sign ...`), and can be checked by multiple commands, notable `helm install --verify`.

#### The Helm Client

The client is responsible for the following domains:

- Local chart development
- Managing repositories
- Interacting with the Tiller server
  - Sending charts to be installed
  - Asking for information about releases
  - Requesting upgrading or uninstalling of existing releases

#### Tiller

Tiller is the in-cluster component of Helm. It interacts directly with the Kubernetes API server to install, upgrade, query, and remove Kubernetes resources. It also stores the objects that represent releases. The server is responsible for the following:

- Listening for incoming requests from the Helm client
- Combining a chart and configuration to build a release
- Installing charts into Kubernetes, and then tracking the subsequent release
- Upgrading and uninstalling charts by interacting with Kubernetes

#### Values (Values Files, values.yaml)

Values provide a way to override template defaults with your own information. Values can be set during `helm install`and `helm upgrade`operations, either by passing them in directly, or by uploading a `values.yaml`file.

The following values are pre-defined, are available to every template, and cannot be overridden. As with all values, the names are case sensitive.

- `Release.Name`: The name of the release (not the chart)
- `Release.Time`: The time the chart release was last updated. This will match the `Last Released` time on a Release object.
- `Release.Namespace`: The namespace the chart was released to.
- `Release.Service`: The service that conducted the release. Usually this is `Tiller`.
- `Release.IsUpgrade`: This is set to true if the current operation is an upgrade or rollback.
- `Release.IsInstall`: This is set to true if the current operation is an install.
- `Release.Revision`: The revision number. It begins at 1, and increments with each `helm upgrade`.
- `Chart`: The contents of the `Chart.yaml`. Thus, the chart version is obtainable as `Chart.Version` and the maintainers are in `Chart.Maintainers`.
- `Files`: A map-like object containing all non-special files in the chart. This will not give you access to templates, but will give you access to additional files that are present (unless they are excluded using `.helmignore`). Files can be accessed using `{{index .Files "file.name"}}` or using the `{{.Files.Get name}}` or `{{.Files.GetString name}}` functions. You can also access the contents of the file as `[]byte` using `{{.Files.GetBytes}}`
- `Capabilities`: A map-like object that contains information about the versions of Kubernetes (`{{.Capabilities.KubeVersion}}`, Tiller (`{{.Capabilities.TillerVersion}}`, and the supported Kubernetes API versions (`{{.Capabilities.APIVersions.Has "batch/v1"`)

A values file can supply values to the chart as well as to any of its dependencies.

#### Hooks

Helm provides a hook mechanism to allow chart developers to intervene at certain points in a release’s life cycle. For example, you can use hooks to:

- Load a ConfigMap or Secret during install before any other charts are loaded.
- Execute a Job to back up a database before installing a new chart, and then execute a second job after the upgrade in order to restore data.
- Run a Job before deleting a release to gracefully take a service out of rotation before removing it.

Hooks are declared as an annotation in the metadata section of a manifest:

```yaml
apiVersion: ...
kind: ....
metadata:
  annotations:
    "helm.sh/hook": "pre-install"
# ...
```

https://helm.sh/docs/developing_charts/#the-available-hooks

#### Templates and functions

Helm uses [Go templates](https://godoc.org/text/template) for templating your resource files.

There are two special template functions: `include` and `required`. The `include` function allows you to bring in another template, and then pass the results to other template functions. The `required` function allows you to declare a particular values entry as required for template rendering. 

The `tpl` function allows developers to evaluate strings as templates inside a template.

The `sha256sum` function can be used to ensure a deployment’s annotation section is updated if another file changes.

#### Chart Tests

A **test** in a helm chart lives under the `templates/` directory and is a pod definition that specifies a container with a  given command to run. The container should exit successfully (exit 0)  for a test to be considered a success. The pod definition must contain  one of the helm test hook annotations: `helm.sh/hook: test-success` or `helm.sh/hook: test-failure`.

Steps to Run a Test Suite on a Release:

```bash
helm install stable/wordpress
helm test RELEASE_NAME
```

## Commands

- ```helm init``` - this will install Tiller to your running Kubernetes cluster. It will also set up any necessary local configuration. It discovers Kubernetes clusters by reading $KUBECONFIG (default ‘~/.kube/config’) and using the default context. To set up just a local environment, use `–client-only`.
	
	Before we set up helm, we need to create an appropriate service account on the cluster with appropriate permissions. Once that is complete we specify the initialization with the appropriate service account.
	
	```bash
	kubectl create serviceaccount --namespace kube-system tiller
	kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
	helm init --service-account tiller
	```
	
	This will set up the tiller deployment. This lives in the `kube-system` namespace. (Watch the deployment with `kubectl get deployments -n kube-system --watch`).

- ```helm completion bash``` - generate autocompletions script for Helm for the specified shell (bash or zsh).
	
- ```helm create``` - create a new chart with the given name:
	
  ```
  foo/
    |- .helmignore        # Contains patterns to ignore when packaging Helm charts.
    |- Chart.yaml         # Information about your chart (contains a Deployment, Ingress and a Service)
    |- values.yaml        # The default values for your templates
    |- charts/            # Charts that this chart depends on
    |- templates/         # The template files
    |- templates/tests/   # The test files
  ```

- ```helm delete [flags] RELEASE-NAME [...]``` - deletes the release from Kubernetes. It removes all of the resources associated with the last release of the chart. Use the `–dry-run` to see which releases will be deleted without actually deleting them.
	
- ```helm dependency``` - manage the dependencies of a chart. For example, this requirements file declares two dependencies:
	
  ```yaml
  # requirements.yaml
  dependencies:
  - name: nginx
    version: "1.2.3"
    repository: "https://example.com/charts"
  - name: memcached
    version: "3.2.1"
    repository: "file://../dependency-chart/nginx"
  ```
  
- ```helm dependency build [flags] CHART``` - build out the charts/ directory from the requirements.lock file.

- ```helm dependency list [flags] CHART``` - list all of the dependencies declared in a chart.

- ```helm dependency update [flags] CHART``` - update the on-disk dependencies to mirror the requirements.yaml file.

- ```helm fetch [flags] [chart URL | repo/chartname] [...]``` - retrieve a package from a package repository, and download it locally.

- ```helm get [flags] RELEASE-NAME``` - prints a human readable collection of information about the chart, the supplied values, and the generated manifest file.

- ```helm get hooks [flags] RELEASE-NAME``` - downloads hooks for a given release. Hooks are formatted in YAML and separated by the YAML `—\n` separator.

- ```helm get manifest [flags] RELEASE-NAME``` - fetches the generated manifest for a given release (YAML-encoded representation of the Kubernetes resources).

- ```helm get notes\values [flags] RELEASE-NAME```

- ```helm history angry-bird``` - prints historical revisions for a given release.

- ```helm inspect [CHART] [flags]``` - prints the contents of the Chart.yaml file and the values.yaml file. Also:
  ```bash
  helm inspect chart [CHART] [flags]
  helm inspect readme [CHART] [flags]
  helm inspect values [CHART] [flags]
  ```

- ```helm install -f myvalues.yaml ./redis``` - installs a chart archive. To override values in a chart, use either the `–values` flag and pass in a file or use the `–set` flag and pass configuration from the command line.

- ```helm lint [flags] PATH``` - takes a path to a chart and runs a series of tests to verify that the chart is well-formed.

- ```helm list ``` - lists only releases that are deployed or failed. Flags like `–deleted` and `–all` will alter this behavior. Filters are regular expressions (Perl compatible) that are applied to the list of releases.

- ```helm package [flags] [CHART-PATH] [...]``` - packages a chart into a versioned chart archive file.

- ```helm plugin``` - manage client-side Helm plugins.

- ```bash
	# Install one or more Helm plugins
	helm plugin install [options] <path|url>... [flags]
	
	# List installed Helm plugins
	helm plugin list [flags]
	
	# Remove one or more Helm plugins
	helm plugin remove <plugin>... [flags]
	
	# Update one or more Helm plugins
	helm plugin update <plugin>... [flags]
	```

- ```helm repo``` - add, list, remove, update, and index chart repositories
  ```bash
  # Add a chart repository
  helm repo add
  
  # Generate an index file given a directory containing packaged charts. This tool is used for creating an ‘index.yaml’ file for a chart repository. 
  helm repo index
  
  # List chart repositories
  helm repo list
  
  # Remove a chart repository
  helm repo remove
  
  # Update information of available charts locally from chart repositories
  helm repo update
  ```

- ```helm reset``` - uninstalls Tiller (the Helm server-side component) from your Kubernetes Cluster and optionally deletes local configuration in $HELM-HOME (default ~/.helm/).

- ```helm rollback [flags] [RELEASE] [REVISION]``` - rolls back a release to a previous revision: `helm rollback [RELEASE] 0` for previous release.

- ```helm search [keyword] [flags]``` - reads through all of the repositories configured on the system, and looks for matches.

- ```helm serve [flags]``` - starts a local chart repository server that serves charts from a local directory.

- ```helm status [flags] RELEASE-NAME``` - status consists of: 
  - last deployment time - k8s namespace in which the release lives.
  - state of the release (can be: UNKNOWN, DEPLOYED, DELETED, SUPERSEDED, FAILED or DELETING).
  - list of resources that this release consists of, sorted by kind.
  - details on last test suite run, if applicable.
  - additional notes provided by the chart.
  
- ```helm template [flags] CHART``` - render chart templates locally and display the output. To render just one template in a chart, use `-x`.

- ```helm test [RELEASE] [flags]``` 

- ```helm upgrade [RELEASE] [CHART] [flags]``` - upgrades a release to a specified version of a chart and/or updates chart values. Use `–version` and `–devel` flags for versions other than latest, `–values/-f` to pass in a yaml file holding settings, `–set` to provide one or more key=val pairs directly, to edit or append to the existing customized values, add the `–reuse-values` flag.

- ```helm verify [flags] PATH``` - verify that the given chart has a valid provenance file. Provenance files provide cryptographic verification that a chart has not been tampered with, and was packaged by a trusted provider.

- ```helm version [flags]``` - show the client and server versions for Helm and tiller.

## The Chart Best Practices Guide

Chart names should use lower case letters and numbers, and start with a letter. Hyphens (-) are allowed. The directory that contains a chart MUST have the same name as the chart.

Wherever possible, Helm uses [SemVer 2](https://semver.org) to represent version numbers.

YAML files should be indented using two spaces (and never tabs).

Variables names should begin with a lowercase letter, and words should be separated with camelcase.

Quote all strings.

The `templates directory` should be structured as follows:

- Template files should have the extension `.yaml` if they produce YAML output. The extension `.tpl` may be used for template files that produce no formatted content.
- Template file names should use dashed notation (`my-example-configmap.yaml`), not camelcase.
- Each resource definition should be in its own template file.
- Template file names should reflect the resource kind in the name. e.g. `foo-pod.yaml`, `bar-svc.yaml`

All defined template names should be namespaced.

Templates should be indented using two spaces (never tabs).

YAML is a superset of JSON. In some cases, using a JSON syntax can be more readable than other YAML representations.

**Requirements Files**

Where possible, use version ranges instead of pinning to an exact  version. The suggested default is to use a patch-level version match:

```yaml
version: ~1.2.3
```

Where possible, use `https://` repository URLs, followed by `http://` URLs.

**Labels and Annotations**

An item of metadata should be a label under the following conditions:

- It is used by Kubernetes to identify this resource
- It is useful to expose to operators for the purpose of querying the system.

## The Chart Template Developer’s Guide

A template directive is enclosed in `{{` and `}}` blocks.

The template directive `{{ .Release.Name }}` injects the release name into the template. The values that are passed into a template can be thought of as namespaced objects, where a dot (`.`) separates each namespaced element.

The leading dot before `Release` indicates that we start with the top-most namespace for this scope.

Helm has over 60 available functions. Some of them are defined by the [Go template language](https://godoc.org/text/template) itself. Most of the others are part of the [Sprig template library](https://godoc.org/github.com/Masterminds/sprig). 

---

Drawing on a concept from UNIX, `pipelines` are a tool for chaining  together a series of template commands to compactly express a series of  transformations.

```yaml
drink: {{ .Values.favorite.drink | quote }}
```

---

`Default`function allows you to specify a default value inside of the  template, in case the value is omitted. Let’s use it to modify the drink example above:

```yaml
drink: {{ .Values.favorite.drink | default "tea" | quote }}
```

Operators are implemented as functions that return a boolean value. To use `eq`, `ne`, `lt`, `gt`, `and`, `or`, `not` etcetera place the operator at the front of the statement followed by  its parameters just as you would a function. To chain multiple  operations together, separate individual functions by surrounding them  with parentheses.

---

The basic structure for a conditional `if`/`else` block:

```yaml
{{ if PIPELINE }}
  # Do something
{{ else if OTHER PIPELINE }}
  # Do something else
{{ else }}
  # Default case
{{ end }}
```

`{{-` (with the dash and space added) indicates that whitespace should be chomped left, while `-}}` means whitespace to the right should be consumed. 

---

`with` actin controls variable scoping:

```yaml
{{ with PIPELINE }}
  # restricted scope
{{ end }}
```

----

`range` the way to iterate through a collection.

```yaml
toppings: |-
    {{- range .Values.pizzaToppings }}
    - {{ . | title | quote }}
    {{- end }}
```

The `|-` marker in YAML takes a multi-line string. This can  be a useful technique for embedding big blocks of data inside of your  manifests, as exemplified here.

---

In Helm templates, a variable is a named reference to another object. It follows the form `$name`. Variables are assigned with a special assignment operator: `:=`. They are scoped to the block in which they are declared. 

---

A `named template` (sometimes called a partial or a subtemplate) is simply a template defined inside of a file, and given a name. Template names are global.

Most files in `templates/` are treated as if they contain Kubernetes manifests. (except `NOTES.txt`).

But files whose name begins with an underscore (`_`) are assumed to not have a manifest inside. These files are not rendered to Kubernetes  object definitions, but are available everywhere within other chart  templates for use. These files are used to store partials and helpers. 

The `define` action allows us to create a named template inside of a template file:

```yaml
{{ define "MY.NAME" }}
  # body of template here
{{ end }}
```

`template` will render that template inline.

**Template names are global**. As a result of this, if two templates are declared with the same name the last occurrence will be the one that is used.

We can pass a scope to the template. Note that we pass `.` at the end of the `template` call.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
  {{- template "mychart.labels" . }}
```

---

It is considered preferable to use `include` over `template` in Helm templates simply so that the output formatting can be handled better for YAML documents.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
  labels:
    {{- include "mychart.app" . | nindent 4 }}
```

---

Helm provides access to files through the `.Files` object.

`.Glob` returns a `Files` type, so you may call any of the `Files` methods on the returned object.

---

Charts can have dependencies, called subcharts, that also have their own values and templates. 

1. A subchart is considered “stand-alone”, which means a subchart can never explicitly depend on its parent chart.
2. For that reason, a subchart cannot access the values of its parent.
3. A parent chart can override values for subcharts.
4. Helm has a concept of global values that can be accessed by all charts.

We can modify subchart values:

```yaml
favorite:
  drink: coffee
  food: pizza
pizzaToppings:
  - mushrooms
  - cheese
  - peppers
  - onions

mysubchart:
  dessert: ice cream
```

### YAML Techniques

In addition to the one-line strings, you can declare multi-line strings:

```yaml
coffee: |
  Latte
  Cappuccino
  Espresso
```

Strip off the trailing newline, add a `-` after the `|`:

```yaml
coffee: |-
  Latte
  Cappuccino
  Espresso
```

Preserve all trailing whitespace with the `|+` notation:

```yaml
coffee: |+
  Latte
  Cappuccino
  Espresso

another: value
```

To declare a folded block, use `>` instead of `|`:

```yaml
coffee: >
  Latte
  Cappuccino
  Espresso
```

It is possible to place more than one YAML documents into a single file. This is done by prefixing a new document with `---` and ending the document with `...`

```yaml
---
document:1
...
---
document: 2
...
```
