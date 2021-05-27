# Top 20 Dockerfile best practices

> References:
>
> https://sysdig.com/blog/dockerfile-best-practices/



#### Avoid unnecessary privileges.

**Avoid running containers as root.**

- Make sure the user specified in the *USER* instruction exists inside the container.
- Provide appropriate file system permissions in the locations where the process will be reading or writing.

**Don't bind to a specific UID.**

Forcing a specific UID (i.e., the first standard user with `UID 1000`) requires adjusting the permissions of any bind mount, like a host  folder for data persistence. Alternatively, if you run the container (`-u` option in docker) with the host UID, it might break the service when trying to read or write from folders within the container.

Run the container as a non-root user, but don't make that user UID a requirement. 

**Make executables owned by root and not writable.**

For every executable in a container to be owned by the root user, even  if it is executed by a non-root user and should not be world-writable. This will block the executing user from modifying existing binaries or scripts.

#### Reduce attack surface.

Avoid including unnecessary packages or exposing ports to reduce the attack surface.

**Leverage multistage builds.**

The final image will contain only the minimal set of libraries.

**Use distroless images, or build your own from scratch.**

Use the minimal required base container, create containers from scratch or use distroless.

- prefer *verified* and *official* **images from trusted repositories**
- check for the image source and the Dockerfile, and **build your own base image**.
- *official* images might not be the **better fit**, in regards to security and minimalism.

**Update your images frequently.**

- **Stick to stable** or long-term support versions
- Be ready to drop old versions and migrate
- **Rebuild your own images periodically** to get the latest packages

**Watch out for exposed ports.**

Expose only the ports that your application needs. 

#### Prevent confidential data leaks.
**Never put secrets or credentials in Dockerfile instructions.**

Environment variables, args, or hard coded into any command.

Be extra careful with files that get copied into the container.

- If the application supports **configuration via environment variables**, use them to set the secrets on execution (-e option in docker run), or use [Docker secrets](https://docs.docker.com/engine/swarm/secrets/), [Kubernetes secrets](https://kubernetes.io/docs/concepts/configuration/secret/) to provide the values as environment variables.
- **Use configuration files** and [bind mount](https://docs.docker.com/storage/bind-mounts/) the configuration files in docker, or [mount them from a Kubernetes secret](https://kubernetes.io/docs/concepts/storage/volumes/#secret).

**Prefer COPY over ADD.**

COPY is more predictable and less error prone.

**Be aware of the Docker context, and use .dockerignore.**

Using “.” as context is dangerous as you can copy confidential or unnecessary files into the container.

Also, create a `.dockerignore` file to explicitly exclude files and directories.

#### Others.
**Reduce the number of layers, and order them intelligently.**

Since RUN, COPY, ADD, and other instructions will create a new container layer, grouping multiple commands together will reduce the number of  layers.

Place the commands that are less likely to change, and easier to cache, first.

Executing a `rm` command removes the file on the next layer,  but it is still available and can be accessed, as the final image  filesystem is composed from all the previous layers.

**Add metadata and labels.**

Labels will help in image management, like including the application  version, a link to the website, how to contact the maintainer, and more. 

**Leverage linters to automatize checks.**

Tools like [Haskell Dockerfile Linter (hadolint)](https://github.com/hadolint/hadolint) can detect bad practices in your Dockerfile, and even expose issues inside the shell commands executed by the `RUN` instruction.

**Scan your images locally during development.**

Perform the scanning at different stages of the image life cycle, in  addition to when the image is already pushed to a container registry. 

**Protect the docker socket and TCP connections.**

Make sure your `/var/run/docker.sock` has the correct permissions, and if docker is exposed via TCP (which is not recommended at all), make sure [it is properly protected](https://docs.docker.com/engine/security/https/).

**Sign your images, and verify them on runtime.**

Digitally sign your images and then verify them on runtime.

```
export DOCKER_CONTENT_TRUST=1
```

**Avoid tag mutability.**

Tags can change unexpectedly, and at any moment.

**Include a health check.**

When using plain Docker or Docker Swarm, [include a HEALTHCHECK instruction](https://docs.docker.com/engine/reference/builder/#healthcheck) .

If running your images in Kubernetes, [use livenessProbe configuration](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) inside the container definitions.

**Restrict your application capabilities.**

Restrict the application capabilities to the minimal required set using `--cap-drop flag` in Docker or[ securityContext.capabilities.drop in Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#set-capabilities-for-a-container). 

