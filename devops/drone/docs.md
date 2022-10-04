# Documentation

> References:
>
> https://docs.drone.io
>

#### Runners

Drone runners poll the server for workloads to execute. There are  different types of runners optimized for different use cases and runtime environments. 

The Exec runner is a daemon that executes build pipelines directly on  the host machine without isolation, using the default shell. This runner is not suitable for un-trusted workloads for security reasons.

The SSH runner executes pipeline commands on a static, remote server  using the SSH protocol. The pipeline commands are executed directly on  the remote server without isolation, using the default shell. This  runner is not suitable for un-trusted workloads for security reasons.

The Digital Ocean runner executes a build pipeline on a dedicated  droplet using the SSH protocol. The droplet is created for each pipeline execution and terminated upon completion.

The MacStadium runner executes a build pipeline inside a dedicated OSX  virtual machine. The virtual machine is created for each pipeline  execution and terminated upon completion.

The Nomad runner is a standalone service that executes Docker pipelines  using the Nomad scheduler. The Nomad runner is functionally equivalent  to the [Docker runner](https://docs.drone.io/runner/docker/overview/) and shares the same pipeline syntax and execution engine.

The Kubernetes runner is a standalone service that executes pipelines  inside Pods. The Kubernetes runner is very similar to the Docker runner, and should be used when running Drone on Kubernetes.

#### CLI

```
brew tap drone/drone
brew install drone

export DRONE_SERVER=https://drone.example.com
export DRONE_TOKEN=xxxxxxxxx

drone info
drone build info example/my-project 42
```

#### Pipelines

- Pipelines help you automate steps in your software delivery process,  such as initiating code builds, running automated tests, and deploying  to a staging or production environment.

- Pipeline execution is triggered by a source code repository. A change in code triggers a webhook to Drone which runs the corresponding pipeline. Other common triggers include automatically scheduled or user-initiated workflows.

- Pipelines are configured by placing a `.drone.yml` file in the root of your git repository. 

- Use the triggers section to limit pipeline execution.

- Use the `platform` section to configure the target operating  system and architecture and routes the pipeline to the appropriate  runner. If unspecified, the system defaults to Linux amd64.

- The workspace is the current working directory for each step in your pipeline. The default workspace path is `/drone/src`.

- Pipeline steps are defined as a series of shell commands. The commands  are executed inside the root directory of your git repository.

- Step commands overrides the docker entrypoint.

- If a command returns a non-zero exit code, the step is marked as failing.

- The environment section provides the ability to define environment variables scoped to individual pipeline steps.

- The when section provides the ability to conditionally limit the execution of steps at runtime.

- The failure attribute lets you customize how the system handles failure of an individual step.

  ```
  failure: ignore
  ```

- The detach attribute lets execute the pipeline step in the background. Note that a detached step cannot fail the pipeline. The runner may ignore the exit code.

- Drone provides the ability to source any configuration parameter from a named secret using the `from_secret` syntax.

- Drone supports launching detached service containers as part of your pipeline.

- Pipeline steps are executed sequentially by default. You can optionally describe your build steps as a [directed acyclic graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph). 

- Conditions can be used to limit pipeline step execution at runtime.

- The `nodes` section can be used to route pipelines to groups of runners with [matching labels](https://docs.drone.io/runner/docker/configuration/reference/drone-runner-labels/).

- You can skip pipeline execution for an individual commit by adding the skip directive to your commit message.

  ```
  git commit -m "update readme [CI SKIP]"
  ```

- Drone provides the ability to expand, or *substitute*, repository and build metadata to facilitate dynamic pipeline configurations.

#### Secrets

- Secrets are not exposed to pull requests by default. This prevents a bad actor from sending a pull request and attempting to expose your  secrets.

#### Promotions

- You can use the promotion feature to promote code to a target environment by build number, for example,  *promote build number 42 to production*.

#### Cron

- You can use cron jobs to execute pipelines on time-based schedules. You can create and manage cron jobs from the repository *Settings* screen, or using the command line tools.

#### Templates

- Drone has build templates that can be shared across projects. A project  can use a template and provide project-specific information to alter the build.

#### Extensions

- Extensions are simple HTTP services that receive and respond to requests using a REST protocol. You can write extensions in any language,  although we do provide starter projects for the Go programming language.

#### Drone Autoscaler

- A standalone daemon that continuously polls your build queue and provisions or terminates server instances based on volume.
- A separate database (or database schema) is required for each autoscaler. They cannot share the same underlying database tables.

#### Command Line Runner

```
brew install drone-cli

drone exec
drone exec --pipeline=test-and-build

drone exec --include=build --include=test
drone exec --exclude=deploy
drone exec --secret-file=secrets.txt
drone exec --branch=master
drone exec --event=pull_request
```

Local execution does not have any communication with the Drone server.

The command line runner mounts your current working directory, using docker volumes, as the working directory of your pipeline steps

Simple text file with secrets defined one per line in key value format can replace secrets.

You can emulate repository metadata by passing repository and build  information to the command line runner using command line flags and  environment variables.

Trusted mode grants a pipeline elevated privileges to your host machine.
