`runc` is a CLI tool for spawning and running containers according to the OCI specification.

In order to use runc you must have your container in the format of an OCI bundle. If you have Docker installed you can use its `export` method to acquire a root filesystem from an existing Docker container.

```bash
# create the top most bundle directory
mkdir /mycontainer
cd /mycontainer

# create the rootfs directory
mkdir rootfs

# export busybox via Docker into the rootfs directory
docker export $(docker create busybox) | tar -C rootfs -xvf -
```

After a root filesystem is populated you just generate a spec in the format of a `config.json` file inside your bundle. `runc`provides a `spec` command to generate a base template spec that you are then able to edit. To find features and documentation for fields in the spec please refer to the [specs](https://github.com/opencontainers/runtime-spec) repository.

```bash
runc spec
```

Now you can execute the container in two different ways:

```bash
# run as root
cd /mycontainer
runc run mycontainerid
```

or

Start a container is using the specs lifecycle operations. This will also launch the container in the background so you will have to edit the `config.json` to remove the `terminal` setting for the simple examples here. Your process field in the `config.json` should look like this below with `"terminal": false` and `"args": ["sleep", "5"]`.

Now we can go through the lifecycle operations in your shell.

```bash
# run as root
cd /mycontainer
runc create mycontainerid

# view the container is created and in the "created" state
runc list

# start the process inside the container
runc start mycontainerid

# after 5 seconds view that the container has exited and is now in the stopped state
runc list

# now delete the container
runc delete mycontainerid
```

`runc` has the ability to run containers without root privileges. This is called `rootless`:

```bash
# Same as the first example
mkdir ~/mycontainer
cd ~/mycontainer
mkdir rootfs
docker export $(docker create busybox) | tar -C rootfs -xvf -

# The --rootless parameter instructs runc spec to generate a configuration for a rootless container, which will allow you to run the container as a non-root user.
runc spec --rootless

# The --root parameter tells runc where to store the container state. It must be writable by the user.
runc --root /tmp/runc run mycontainerid
```