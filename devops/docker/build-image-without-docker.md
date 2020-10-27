#### kaniko

https://github.com/GoogleContainerTools/kaniko

Is a tool to build container images from a Dockerfile, inside a container or Kubernetes cluster.

Does not depend on a Docker daemon and executes each command  within a Dockerfile completely in userspace. This enables building container images in environments that can't easily or securely run a Docker daemon, such as a standard Kubernetes cluster.

Running kaniko in any Docker image other than the official kaniko image is not supported (ie YMMV).

To use kaniko to build and push an image for you, you will need:

1. A [build context](https://github.com/GoogleContainerTools/kaniko#kaniko-build-contexts):
   1. GCS Bucket
   2. S3 Bucket
   3. Azure Blob Storage
   4. Local Directory
   5. Local Tar
   6. Standard Input
   7. Git Repository
2. A [running instance of kaniko](https://github.com/GoogleContainerTools/kaniko#running-kaniko)
   1. [In a Kubernetes cluster](https://github.com/GoogleContainerTools/kaniko#running-kaniko-in-a-kubernetes-cluster)
   2. [In gVisor](https://github.com/GoogleContainerTools/kaniko#running-kaniko-in-gvisor)
   3. [In Google Cloud Build](https://github.com/GoogleContainerTools/kaniko#running-kaniko-in-google-cloud-build)
   4. [In Docker](https://github.com/GoogleContainerTools/kaniko#running-kaniko-in-docker)

Does not actually create nested containers, so it does not require seccomp and AppArmor to be disabled.

Does not use `runc` so it doesn't require the use of kernel namespacing techniques.


#### buildkit

https://github.com/moby/buildkit

BuildKit is composed of the `buildkitd` daemon and the `buildctl` client. While the `buildctl` client is available for Linux, macOS, and Windows, the `buildkitd` daemon is only available for Linux currently.

The `buildkitd` daemon requires the following components to be installed:
- [runc](https://github.com/opencontainers/runc) or [crun](https://github.com/containers/crun)
- [containerd](https://github.com/containerd/containerd) (if you want to use containerd worker)

Rootless mode https://github.com/moby/buildkit/blob/master/docs/rootless.md.

Can perform as a non-root user from within a container but requires seccomp and AppArmor to be disabled to create nested containers.


#### buildx

https://github.com/docker/buildx


#### Vab

https://github.com/stellarproject/vab


#### img

https://github.com/genuinetools/img

Can perform as a non-root user from within a container but requires seccomp and AppArmor to be disabled to create nested containers.


#### orca-build

https://github.com/cyphar/orca-build

Depends on `runc` to build images from Dockerfiles, which can not run inside a container.

Does not require Docker or any privileged daemon (so builds can be done entirely without privilege).


#### umoci

https://github.com/opencontainers/umoci

Works without any privileges, and also has no restrictions on the root filesystem being extracted (though it requires additional handling if your filesystem is sufficiently complicated). However, it has no `Dockerfile`-like build tooling (it's a slightly lower-level tool that can be used to build such builders).


#### buildah

https://github.com/containers/buildah

Specializes in building OCI images. Buildah's commands replicate all of the commands that are found in a Dockerfile.  This allows building images with and without Dockerfiles while not requiring any root privileges. Buildah’s ultimate goal is to provide a lower-level coreutils interface to build images.  The flexibility of building images without Dockerfiles allows for the integration of other scripting languages into the build process. Buildah follows a simple fork-exec model and does not run as a daemon but it is based on a comprehensive API in golang, which can be vendored into other tools.


#### FTL

https://github.com/GoogleCloudPlatform/runtimes-common/tree/master/ftl


#### Bazel

https://github.com/bazelbuild/rules_docker


#### DinD

https://hub.docker.com/_/docker


#### Cloud Build

https://cloud.google.com/cloud-build/


#### Container Registry

https://azure.microsoft.com/en-us/services/container-registry/


#### source-to-image

https://github.com/openshift/source-to-image


#### Tekton

https://tekton.dev/

https://github.com/tektoncd/pipeline

https://github.com/tektoncd/catalog


#### sanic

https://github.com/distributed-containers-inc/sanic


#### rio

https://github.com/rancher/rio


#### pouch

https://github.com/alibaba/pouch


#### okteto

https://okteto.com/


#### earthly

https://github.com/earthly/earthly

Can Earthly build Dockerfiles? It cannot - however, translating Dockerfiles to Earthfiles (`build.earth`) is usually a matter of copy-pasting and making small adjustments. See the [examples](https://docs.earthly.dev/guides/basics).


#### k3c

https://github.com/rancher/k3c


#### makisu

https://github.com/uber/makisu