# Docker Architecture

From [https://docs.docker.com/](https://docs.docker.com/)

* Client-server:
  * The Docker daemon builds, runs, and distributes the containers
  * Client: Docker client; Server: Docker daemon
  * Both can run on the same system, or the Docker client can be connected to a remote Docker daemon
  * Communication uses REST API, over UNIX sockets or a network interface.
  * Another Docker client is Docker Compose which lets you work with applications consisting of a set of containers.

## The Docker Daemon

* The Docker daemon (`dockerd`):
  * listens for Docker API requests
  * manages Docker objects such as images, containers, networks, and volumes
  * can also communicate with other daemons to manage Docker services

## The Docker Client

* The Docker client (`docker`) is the primary way that many Docker users interact with Docker.
  * When you use commands such as `docker run`, the client sends these commands to dockerd, which carries them out.
* The docker command uses the Docker API.
* The Docker client can communicate with more than one daemon.

## Docker registries

* A Docker registry stores Docker images.
* Docker Hub is a public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default.
* You can run your own private registry.
* When you use the `docker pull` or `docker run` commands, the required images are pulled from your configured registry.
* When you use the `docker push` command, your image is pushed to your configured registry.

## Docker Objects

* List of objects: images, containers, networks, volumes, plugins, and other objects.

### Images

* An image is a read-only template with instructions for creating a Docker container.
* Often, an image is based on another image, with some additional customization.
  * Example: you may build an image which is based on the ubuntu image, but installs the Apache web server and your application, as well as the configuration details needed to make your application run.
* You might create your own images or you might only use those created by others and published in a registry.
* To build your own image, you create a Dockerfile with a simple syntax for defining the steps needed to create the image and run it.
  * Each instruction in a Dockerfile creates a layer in the image. When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt.
    * This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.

### Containers

* A container is a runnable instance of an image.
* You can create, start, stop, move, or delete a container using the Docker API or CLI.
* You can
  * connect a container to one or more networks,
  * attach storage to it, or even
  * create a new image based on its current state.
* A container is defined by its image as well as any configuration options you provide to it when you create or start it. When a container is removed, any changes to its state that are not stored in persistent storage disappear.

### Example `docer run` command

```sh
$ docker run -i -t ubuntu /bin/bash
```

Steps happen:

1. If you do not have the `ubuntu` image locally, Docker pulls it from your configured registry, as though you had run `docker pull ubuntu` manually.
2. Docker creates a new container, as though you had run a `docker container create` command manually.
3. Docker allocates a read-write filesystem to the container, as its final layer. This allows a running container to create or modify files and directories in its local filesystem.
4. Docker creates a network interface to connect the container to the default network, since you did not specify any networking options. This includes assigning an IP address to the container. By default, containers can connect to external networks using the host machine’s network connection.
5. Docker starts the container and executes `/bin/bash`. Because the container is running interactively and attached to your terminal (due to the `-i` and `-t` flags), you can provide input using your keyboard while the output is logged to your terminal.
6. When you type `exit` to terminate the `/bin/bash` command, the container stops but is not removed. You can start it again or remove it.


## Underlying Technology

* Docker:
  * is written in Go
  * takes advantages of several features of the Linux kernel to deliver its functionality
  * uses a technology called `namespaces` to provide the isolated workspace called the *container*.
    * When you run a container, Docker creates a set of namespaces for that container.
    * These namespaces provide a layer of isolation. Each aspect of a container runs in a separate namespace and its access is limited to that namespace.
