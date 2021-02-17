### ARGO

Argo is a collection of tools for getting work done with Kubernetes.

- [Argo Workflows](https://github.com/argoproj/argo)- Container-native Workflow Engine.
- [Argo CD](https://github.com/argoproj/argo-cd)- Declarative GitOps Continuous Delivery (GitOps is a way of handling deployments, meaning that git repositories are the single source of truth and that your Kubernetes cluster mirrors everything from those repositories).
- [Argo Events](https://github.com/argoproj/argo-events)- Event-based Dependency Manager.
- [Argo Rollouts](https://github.com/argoproj/argo-rollouts)- Deployment CR with support for Canary and Blue Green deployment strategies.

#### CLI commands

```bash
Available Commands:
  delete      delete a workflow and its associated pods
  get         display details about a workflow
  lint        validate a file or directory of workflow manifests
  list        list workflows
  logs        view logs of a workflow
  resubmit    resubmit a workflow
  resume      resume a workflow
  retry       retry a workflow
  submit      submit a workflow
  suspend     suspend a workflow
  terminate   terminate a workflow
  version     Print version information
  wait        waits for a workflow to complete
  watch       watch a workflow until it completes
```

## Workflows

Container-native workflow engine for orchestrating parallel jobs on Kubernetes (as kubernetes CRD (Custom Resource Definition)):

- Define workflows where each step in the workflow is a container.
- Model multi-step workflows as a sequence of tasks or capture the dependencies between tasks using a graph (DAG).
- Easily run compute intensive jobs for machine learning or data processing in a fraction of the time.
- Run CI/CD pipelines natively.

#### Features

```text
- DAG or Steps based declaration of workflows
- Artifact support (S3, Artifactory, HTTP, Git, raw)
- Step level input & outputs (artifacts/parameters)
- Loops
- Parameterization
- Conditionals
- Timeouts (step & workflow level)
- Retry (step & workflow level)
- Resubmit (memoized)
- Suspend & Resume
- Cancellation
- K8s resource orchestration
- Exit Hooks (notifications, cleanup)
- Garbage collection of completed workflow
- Scheduling (affinity/tolerations/node selectors)
- Volumes (ephemeral/existing)
- Parallelism limits
- Daemoned steps
- DinD (docker-in-docker)
- Script steps
```

#### Demo

```bash
# Install
brew install argoproj/tap/argo

# Install the Controller and UI
kubectl create namespace argo
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/stable/manifests/install.yaml

# Grant your account the ability to create new clusterroles
kubectl create clusterrolebinding YOURNAME-cluster-admin-binding --clusterrole=cluster-admin --user=YOUREMAIL@gmail.com

# Configure the service account to run workflows (for demo purposes)
kubectl create rolebinding default-admin --clusterrole=admin --serviceaccount=default:default

# Run Simple Example Workflows
argo submit --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/hello-world.yaml
argo submit --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/coinflip.yaml
argo submit --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/loops-maps.yaml
argo list
argo get xxx-workflow-name-xxx
argo logs xxx-pod-name-xxx # from get command above

# Install an Artifact Repository
helm install stable/minio \
  --name argo-artifacts \
  --set service.type=LoadBalancer \
  --set defaultBucket.enabled=true \
  --set defaultBucket.name=my-bucket \
  --set persistence.enabled=false \
  --set fullnameOverride=argo-artifacts

# Login to the Minio UI using a web browser (port 9000) after exposing obtaining the external IP using kubectl
kubectl get service argo-artifacts-mini -o wide
# on minikube
minikube service --url argo-artifacts-mini

# Reconfigure the workflow controller to use the Minio artifact repository
kubectl edit cm -n argo workflow-controller-configmap
...
data:
  config: |
    artifactRepository:
      s3:
        bucket: my-bucket
        endpoint: argo-artifacts.default:9000
        insecure: true
        # accessKeySecret and secretKeySecret are secret selectors.
        # It references the k8s secret named 'argo-artifacts'
        # which was created during the minio helm install. The keys,
        # 'accesskey' and 'secretkey', inside that secret are where the
        # actual minio credentials are stored.
        accessKeySecret:
          name: argo-artifacts
          key: accesskey
        secretKeySecret:
          name: argo-artifacts
          key: secretkey

# Run a workflow which uses artifacts
argo submit https://raw.githubusercontent.com/argoproj/argo/master/examples/artifact-passing.yaml

# Access the Argo UI: http://127.0.0.1:8001
kubectl -n argo port-forward deployment/argo-ui 8001:8001
# on minikube
minikube service -n argo --url argo-ui
```

#### Workflow Templates 

Argo is an open source project that provides container-native workflows for Kubernetes. Each step in an Argo workflow is defined as a container. Argo workflows can be managed using `kubectl`and natively integrates with other Kubernetes services such as volumes, secrets, and RBAC.

The Structure of Workflow Specs:

- Kubernetes header including metadata
- Spec body
  - Entrypoint invocation with optionally arguments
  - List of template definitions
- For each template definition
  - Name of the template
  - Optionally a list of inputs
  - Optionally a list of outputs
  - Container invocation (leaf template) or a list of steps
    - For each step, a template invocation

```yaml
# Using the docker/whalesay container
docker run docker/whalesay cowsay "hello world"

# Argo adds a new kind of Kubernetes spec called a Workflow. The above spec contains a single template called whalesay which runs the docker/whalesay container and invokes cowsay "hello world". The whalesay template is the entrypoint for the spec. The entrypoint specifies the initial template that should be invoked when the workflow spec is executed by Kubernetes.
apiVersion: argoproj.io/v1alpha1
kind: Workflow                  # new type of k8s spec
metadata:
  generateName: hello-world-    # name of the workflow spec
spec:
  entrypoint: whalesay          # invoke the whalesay template
  templates:
  - name: whalesay              # name of the template
    container:
      image: docker/whalesay
      command: [cowsay]
      args: ["hello world"]
      resources:                # limit the resources
        limits:
          memory: 32Mi
          cpu: 100m
```

##### Parameters

```yaml
# Complex workflow spec with parameters which are globally scoped and can be accessed via {{workflow.parameters.parameter_name}}. This can be useful to pass information to multiple steps in a workflow.
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: hello-world-parameters-
spec:
  # invoke the whalesay template with
  # "hello world" as the argument
  # to the message parameter
  entrypoint: whalesay
  arguments:
    parameters:
    - name: message
      value: hello world

  templates:
  - name: whalesay
    inputs:
      parameters:
      - name: message       # parameter declaration
    container:
      # run cowsay with that message input parameter as args
      image: docker/whalesay
      command: [cowsay]
      args: ["{{inputs.parameters.message}}"]

# override parameters
argo submit arguments-parameters.yaml -p message="goodbye world"
argo submit arguments-parameters.yaml --parameter-file params.yaml

# override entrypoint
argo submit arguments-parameters.yaml --entrypoint whalesay-caps
```

##### Steps

```yaml
# Create multi-step workflows
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: steps-
spec:
  entrypoint: hello-hello-hello

  # This spec contains two templates: hello-hello-hello and whalesay
  templates:
  - name: hello-hello-hello
    # Instead of just running a container
    # This template has a sequence of steps
    steps:
    - - name: hello1            # hello1 is run before the following steps
        template: whalesay
        arguments:
          parameters:
          - name: message
            value: "hello1"
    - - name: hello2a           # double dash => run after previous step
        template: whalesay
        arguments:
          parameters:
          - name: message
            value: "hello2a"
      - name: hello2b           # single dash => run in parallel with previous step
        template: whalesay
        arguments:
          parameters:
          - name: message
            value: "hello2b"

  # This is the same template as from the previous example
  - name: whalesay
    inputs:
      parameters:
      - name: message
    container:
      image: docker/whalesay
      command: [cowsay]
      args: ["{{inputs.parameters.message}}"]
```

##### DAG

```yaml
# define the workflow as a directed-acyclic graph (DAG)
# step A runs first, as it has no dependencies. Once A has finished, steps B and C run in parallel. Finally, once B and C have completed, step D can run.
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: dag-diamond-
spec:
  entrypoint: diamond
  templates:
  - name: echo
    inputs:
      parameters:
      - name: message
    container:
      image: alpine:3.7
      command: [echo, "{{inputs.parameters.message}}"]
  - name: diamond
    dag:
      tasks:
      - name: A
        template: echo
        arguments:
          parameters: [{name: message, value: A}]
      - name: B
        dependencies: [A]
        template: echo
        arguments:
          parameters: [{name: message, value: B}]
      - name: C
        dependencies: [A]
        template: echo
        arguments:
          parameters: [{name: message, value: C}]
      - name: D
        dependencies: [B, C]
        template: echo
        arguments:
          parameters: [{name: message, value: D}]

# The dependency graph may have multiple roots.
# The DAG logic has a built-in fail fast feature to stop scheduling new steps, as soon as it detects that one of the DAG nodes is failed.
```

##### Artifacts

```yaml
# The first step named generate-artifact will generate an artifact using the whalesay template that will be consumed by the second step named print-message that then consumes the generated artifact.
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: artifact-passing-
spec:
  entrypoint: artifact-example
  templates:
  - name: artifact-example
    steps:
    - - name: generate-artifact
        template: whalesay
    - - name: consume-artifact
        template: print-message
        arguments:
          artifacts:
          # bind message to the hello-art artifact
          # generated by the generate-artifact step
          - name: message
            from: "{{steps.generate-artifact.outputs.artifacts.hello-art}}"

  - name: whalesay
    container:
      image: docker/whalesay:latest
      command: [sh, -c]
      args: ["cowsay hello world | tee /tmp/hello_world.txt"]
    outputs:
      artifacts:
      # generate hello-art artifact from /tmp/hello_world.txt
      # artifacts can be directories as well as files
      - name: hello-art
        path: /tmp/hello_world.txt

  - name: print-message
    inputs:
      artifacts:
      # unpack the message input artifact
      # and put it at /tmp/message
      - name: message
        path: /tmp/message
    container:
      image: alpine:latest
      command: [sh, -c]
      args: ["cat /tmp/message"]
```

##### Secrets

Argo supports the same secrets syntax and mechanisms as Kubernetes Pod specs, which allows access to secrets as environment variables or volume mounts. See the [Kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/secret/)for more information.

##### Scripts & Results

Often, we just want a template that executes a script specified as a here-script (also known as a `here document`) in the workflow spec.

```yaml
 - name: gen-random-int-bash
    script:
      image: debian:9.4
      command: [bash]
      source: |                                         # Contents of the here-script
        cat /dev/urandom | od -N2 -An -i | awk -v f=1 -v r=100 '{printf "%i\n", f + r * $1 / 65536}'
```

This creates a temporary file containing the script body and then passes the name of the temporary file as the final parameter to `command`, which should be an interpreter that executes the script body.

The use of the `script`feature also assigns the standard output of running the script to a special output parameter named `result`.

##### Output Parameters

Output parameters provide a general mechanism to use the result of a step as a parameter rather than as an artifact. This allows you to use the result from any type of step, not just a `script`, for conditional tests, loops, and arguments.

```yaml
- name: whalesay
    container:
      image: docker/whalesay:latest
      command: [sh, -c]
      args: ["echo -n hello world > /tmp/hello_world.txt"]  # generate the content of hello_world.txt
    outputs:
      parameters:
      - name: hello-param		# name of output parameter
        valueFrom:
          path: /tmp/hello_world.txt	# set the value of hello-param to the contents of this hello-world.txt
```

##### Loops

When writing workflows, it is often very useful to be able to iterate over a set of inputs.

```yaml
- - name: print-message
        template: whalesay
        arguments:
          parameters:
          - name: message
            value: "{{item}}"
        withItems:              # invoke whalesay once for each item in parallel
        - hello world           # item 1
        - goodbye world         # item 2
```

##### Conditionals

```yaml
steps:
    # flip a coin
    - - name: flip-coin
        template: flip-coin
    # evaluate the result in parallel
    - - name: heads
        template: heads                 # call heads template if "heads"
        when: "{{steps.flip-coin.outputs.result}} == heads"
      - name: tails
        template: tails                 # call tails template if "tails"
        when: "{{steps.flip-coin.outputs.result}} == tails"
```

##### Recursion

Templates can recursively invoke each other

```yaml
steps:
    # flip a coin
    - - name: flip-coin
        template: flip-coin
    # evaluate the result in parallel
    - - name: heads
        template: heads                 # call heads template if "heads"
        when: "{{steps.flip-coin.outputs.result}} == heads"
      - name: tails                     # keep flipping coins if "tails"
        template: coinflip
        when: "{{steps.flip-coin.outputs.result}} == tails"
```

##### Exit handlers

An exit handler is a template that *always*executes, irrespective of success or failure, at the end of the workflow.

```yaml
templates:
  # primary workflow template
  - name: intentional-fail
    container:
      image: alpine:latest
      command: [sh, -c]
      args: ["echo intentional failure; exit 1"]

  # Exit handler templates
  # After the completion of the entrypoint template, the status of the
  # workflow is made available in the global variable {{workflow.status}}.
  # {{workflow.status}} will be one of: Succeeded, Failed, Error
  - name: exit-handler
    steps:
    - - name: notify
        template: send-email
      - name: celebrate
        template: celebrate
        when: "{{workflow.status}} == Succeeded"
      - name: cry
        template: cry
        when: "{{workflow.status}} != Succeeded"
```

##### Timeouts

To limit the elapsed time for a workflow, you can set the variable `activeDeadlineSeconds`

```yaml
 - name: sleep
    container:
      image: alpine:latest
      command: [sh, -c]
      args: ["echo sleeping for 1m; sleep 60; echo done"]
    activeDeadlineSeconds: 10           # terminate container template after 10 seconds
```

##### Volumes

Volumes are a very useful way to move large amounts of data from one step in a workflow to another. 

```yaml
volumeClaimTemplates:                 # define volume, same syntax as k8s Pod spec
  - metadata:
      name: workdir                     # name of volume claim
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi                  # Gi => 1024 * 1024 * 1024
```

##### Daemon Containers

Argo workflows can start containers that run in the background (also known as `daemon containers`) while the workflow itself continues execution. The big advantage of daemons compared with sidecars is that their existence can persist across multiple steps or even the entire workflow.

```yaml
 - name: influxdb
    daemon: true                        # start influxdb as a daemon
    container:
      image: influxdb:1.2
      restartPolicy: Always             # restart container if it fails
      readinessProbe:                   # wait for readinessProbe to succeed
        httpGet:
          path: /ping
          port: 8086
```

##### Sidecars

A sidecar is another container that executes concurrently in the same pod as the main container and is useful in creating multi-container pods.

```yaml
templates:
  - name: sidecar-nginx-example
    container:
      image: appropriate/curl
      command: [sh, -c]
      # Try to read from nginx web server until it comes up
      args: ["until `curl -G 'http://127.0.0.1/' >& /tmp/out`; do echo sleep && sleep 1; done && cat /tmp/out"]
    # Create a simple nginx web server
    sidecars:
    - name: nginx
      image: nginx:1.13
```

##### Hardwired Artifacts

We find certain types of artifacts are very common, so there is built-in support for git, http, and s3 artifacts.

```yaml
artifacts:
  # Check out the master branch of the argo repo and place it at /src
  # revision can be anything that git checkout accepts: branch, commit, tag, etc.
  - name: argo-source
    path: /src
    git:
      repo: https://github.com/argoproj/argo.git
      revision: "master"  
  # Copy an s3 bucket and place it at /s3
  - name: objects
    path: /s3
    s3:
      endpoint: storage.googleapis.com
      bucket: my-bucket-name
      key: path/in/bucket
      accessKeySecret:
        name: my-s3-credentials
        key: accessKey
      secretKeySecret:
        name: my-s3-credentials
        key: secretKey
```

##### Kubernetes Resources

In many cases, you will want to manage Kubernetes resources from Argo workflows. The resource template allows you to create, delete or updated any type of Kubernetes resource.

```yaml
templates:
  - name: pi-tmpl
    resource:                   # indicates that this is a resource template
      action: create            # can be any kubectl action (e.g. create, delete, apply, patch)
      # The successCondition and failureCondition are optional expressions.
      # If failureCondition is true, the step is considered failed.
      # If successCondition is true, the step is considered successful.
      # They use kubernetes label selection syntax and can be applied against any field
      # of the resource (not just labels). Multiple AND conditions can be represented by comma
      # delimited expressions.
      # For more details: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
      successCondition: status.succeeded > 0
      failureCondition: status.failed > 3
      manifest: |               #put your kubernetes spec here
        apiVersion: batch/v1
        kind: Job
        metadata:
          generateName: pi-job-
        spec:
          template:
            metadata:
              name: pi
            spec:
              containers:
              - name: pi
                image: perl
                command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
              restartPolicy: Never
          backoffLimit: 4
```

##### Docker-in-Docker Using Sidecars

DinD is useful when you want to run Docker commands from inside a container. 

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: sidecar-dind-
spec:
  entrypoint: dind-sidecar-example
  templates:
  - name: dind-sidecar-example
    container:
      image: docker:17.10
      command: [sh, -c]
      args: ["until docker ps; do sleep 3; done; docker run --rm debian:latest cat /etc/os-release"]
      env:
      - name: DOCKER_HOST           # the docker daemon can be access on the standard port on localhost
        value: 127.0.0.1
    sidecars:
    - name: dind
      image: docker:17.10-dind      # Docker already provides an image for running a Docker daemon
      securityContext:
        privileged: true            # the Docker daemon can only run in a privileged container
      # mirrorVolumeMounts will mount the same volumes specified in the main container
      # to the sidecar (including artifacts), at the same mountPaths. This enables
      # dind daemon to (partially) see the same filesystem as the main container in
      # order to use features such as docker volume binding.
      mirrorVolumeMounts: true
```

##### Custom Template Variable Reference

We can use the other template language variable reference (E.g: Jinja) in Argo workflow template. Argo will validate and resolve only the variable that starts with Argo allowed prefix {"item", "steps", "inputs", "outputs", "workflow", "tasks"}

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: custom-template-variable-
spec:
  entrypoint: hello-hello-hello

  templates:
    - name: hello-hello-hello
      steps:
        - - name: hello1
            template: whalesay
            arguments:
              parameters: [{name: message, value: "hello1"}]
        - - name: hello2a
            template: whalesay
            arguments:
              parameters: [{name: message, value: "hello2a"}]
          - name: hello2b
            template: whalesay
            arguments:
              parameters: [{name: message, value: "hello2b"}]

    - name: whalesay
      inputs:
        parameters:
          - name: message
      container:
        image: docker/whalesay
        command: [cowsay]
        args: ["{{user.username}}"]
```

## Argo CD

Argo CD is a declarative, GitOps continuous delivery tool for Kubernetes. It follows the **GitOps** pattern of using Git repositories as the source of truth for defining the desired application state. Kubernetes manifests can be specified in several ways:

- [kustomize](https://kustomize.io/) applications
- [helm](https://helm.sh/) charts
- [ksonnet](https://ksonnet.io/) applications
- [jsonnet](https://jsonnet.org/) files 
- Plain directory of YAML/json manifests
- Any custom config management tool configured as a config management plugin

Argo CD automates the deployment of the desired application states in the specified target environments. Application deployments can track updates to branches, tags, or pinned to a specific version of manifests at a Git commit. 

![Argo CD Architecture](.argo-images/argocd_architecture.png)

Argo CD is implemented as a kubernetes controller which continuously monitors running applications and compares the current, live state against the desired target state (as specified in the Git repo). A deployed application whose live state deviates from the target state is considered `OutOfSync`. Argo CD reports & visualizes the differences, while providing facilities to automatically or manually sync the live state back to the desired target state. Any modifications made to the desired target state in the Git repo can be automatically applied and reflected in the specified target environments.

## Argo Events

Event-based dependency manager for Kubernetes which helps you define multiple dependencies from a variety of event sources like webhook, s3, schedules, streams etc. and trigger Kubernetes objects after successful event dependencies resolution.

The framework is made up of two components: 

1. **Gateway** which is implemented as a Kubernetes-native Custom Resource Definition processes events from event source.
2. **Sensor **which is implemented as a Kubernetes-native Custom Resource Definition defines a set of event dependencies and triggers K8s resources.
3. **Event Source** is a configmap that contains configurations which is interpreted by gateway as source for events producing entity. 

Gateway monitors event sources and starts routines in parallel that consume events from entities like S3, Github, SNS, SQS, PubSub etc. and dispatch these events to sensor. Sensor upon receiving the events, evaluates the dependencies and triggers Argo workflows or other K8s resources.

## Rollouts

A new custom resource called a Rollout to provide additional deployment strategies such as Blue Green and Canary to Kubernetes.

Similar to the deployment object, the Argo Rollouts controller will manage the creation, scaling, and deletion of ReplicaSets. These ReplicaSets are defined by the `spec.template`field, which uses the same pod template as the deployment object. When the `spec.template`is changed, that signals to the Argo Rollouts controller that a new ReplicaSet will be introduced. The controller will use the strategy set within the `spec.strategy`field in order to determine how the rollout will progress from the old ReplicaSet to the new ReplicaSet. Once that new ReplicaSet has successfully progressed into the stable version, that Rollout will be marked as the stable ReplicaSet. If another change occurs in the `spec.template`during a transition from a stable ReplicaSet to a new ReplicaSet. The previously new ReplicaSet will be scaled down, and the controller will try to progress the ReplicasSet that reflects the `spec.template`field.
