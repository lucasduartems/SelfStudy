
# About this document

This document summarizes my *Docker* self study based on [Play with Docker classroom](https://training.play-with-docker.com).

# Table of contents


- [About this document](#about-this-document)
- [Ways of running containers](#ways-of-running-containers)
  * [Running a single task](#running-a-single-task)
  * [Running an interactive container](#running-an-interactive-container)
  * [Running in the background](#running-in-the-background)
    + [Executing a command inside a running container](#executing-a-command-inside-a-running-container)
- [Dockerfiles](#dockerfiles)
    + [What each line means](#what-each-line-means)
- [Commands quick reference](#commands-quick-reference)
    + [*docker container run* command flags](#-docker-container-run--command-flags)
- [Dockerfile instructions quick reference](#dockerfile-instructions-quick-reference)
- [References](#references)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

# Ways of running containers

There are three different ways of running containers:

 1. Running a single task
 2. Running interactively
 3. Running in the background

## Running a single task

The following command starts a container with the *Alpine Linux* image and runs the `hostname` command:

```
docker container run alpine hostname
```

## Running an interactive container

The following command starts a container with the *Ubuntu Linux* image in an interactive fashion and runs the `bash` command.

It also allocates a *pseudo-tty*. This way, one can interact with the container's shell.

The `--rm` flag causes the container automatic removal after it stops running.

```
docker container run --interactive --tty --rm ubuntu bash
```

**Example:** Showing the Linux distribution the container is running
```
$ docker container run --interactive --tty --rm ubuntu bash
root@a59535478a1b:/#
root@a59535478a1b:/# cat /etc/issue
Ubuntu 18.04.1 LTS \n \l

root@a59535478a1b:/# exit
$
```

## Running in the background

The following command runs a container in the background (by using the `--detach` flag) with the *latest* `mysql` image.

It also gives the container a name (by using the `--name` flag) and creates an *environment variable* with the `-e` flag.

```
docker container run --detach --name mydb \
 -e MYSQL_ROOT_PASSWORD=my-secret-password mysql:latest
```

**Example:** checking that the *mydb* container is running
```
$ docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                 NAMES
41b56d5d2878        mysql:latest        "docker-entrypoint.s…"   About a minute ago   Up About a minute   3306/tcp, 33060/tcp   mydb
```

### Executing a command inside a running container

The following command will check the MySQL version by executing the `mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version` command inside the running *mydb* container.
```
$ docker container exec mydb mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version
mysql  Ver 8.0.12 for Linux on x86_64 (MySQL Community Server - GPL)
mysql: [Warning] Using a password on the command line interface can be insecure.
```

**Example:** running the same command by using an *interactive shell* inside the *mydb* container
```
$ docker container exec -it mydb sh
# mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version
mysql: [Warning] Using a password on the command line interface can be insecure.
mysql  Ver 8.0.12 for Linux on x86_64 (MySQL Community Server - GPL)
# exit
$
```

# Dockerfiles

A *Dockerfile* is a kind of blueprint for *creating Docker images*.

An example is shown below. The image it describes, when started in a container, will host a simple web page.

```
 FROM nginx:latest

 COPY index.html /usr/share/nginx/html
 COPY linux.png /usr/share/nginx/html

 EXPOSE 80 443

 CMD ["nginx", "-g", "daemon off;"]
```

### What each line means
* `FROM` specifies a *base image* (a starting point), which in this case is the latest NGINX image;
* `COPY` copies a file from the current directory (on host OS) to the container's specified path;
* `EXPOSE` lists the ports the application uses;
* `CMD` specifies the command (with arguments) to be run once the container starts;

To build an image from the Dockerfile, the following command can be used:
```
docker image build [--tag <repository_name>:<tag>] .
```

The ending dot (`.`) is important, as it corresponds to the build context (in this case, the current directory).

**Example:** building an image from the Dockerfile, viewing the created image, starting a container from it and accessing the webpage


```
$ docker image build --tag my_docker_id/sample_web_page:1.0 .
Sending build context to Docker daemon  114.2kB
Step 1/5 : FROM nginx:latest
 ---> dbfc48660aeb
Step 2/5 : COPY index.html /usr/share/nginx/html
 ---> 1c424091b234
Step 3/5 : COPY linux.png /usr/share/nginx/html
 ---> 46ac755c180f
Step 4/5 : EXPOSE 80 443
 ---> Running in 74da5fd78f46
Removing intermediate container 74da5fd78f46
 ---> 3b1e7a69dfdf
Step 5/5 : CMD ["nginx", "-g", "daemon off;"]
 ---> Running in c7e6dafa5e1d
Removing intermediate container c7e6dafa5e1d
 ---> 29f6f45916d5
Successfully built 29f6f45916d5
Successfully tagged my_docker_id/sample_web_page:1.0
```
```
$ docker images
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
my_docker_id/sample_web_page   1.0                 29f6f45916d5        37 seconds ago      109MB
mysql                          latest              ee1e8adfcefb        2 days ago          484MB
nginx                          latest              dbfc48660aeb        2 days ago          109MB
```
```
$ docker container run --detach --publish 80:80 --name sample_web_page my_docker_id/sample_web_page:1.0
30c6e31d21ec56fa1529653b72363c3557e67a2719925be1635b157020eec2e5
```
```
$ curl 127.0.0.1:80
<html>
    <head>
        <style>

        body {
        	background-image: linear-gradient(-74deg, transparent 90%, rgba(255, 255, 255, 0.23) 20%),

[...]
```

The `--publish` flag maps network traffic between the host and the container, and its format is `--publish host_port:container_port`.

For example, `--publish 80:80` will redirect the external traffic coming in to the host's `80` port to the container's `80` port.

**Example:** mounting the host's current directory into the container
```
docker container run \
 --detach \
 --publish 80:80 \
 --name=sample_web_page \
 --mount type=bind,source="$(pwd)",target=/usr/share/nginx/html \
 my_docker_id/sample_web_page:1.0
```

When the container is running, any changes made to the current directory (on host machine) will be reflected inside the container's directory `/usr/share/nginx/html`.

# Commands quick reference

|Action|Command  |
|--|--|
|List [all] containers|`docker container ls [--all]`|
|List [all] containers|`docker ps [-a]`|
|[Force] remove a container|`docker rm [--force] <container_id>`|
|List docker images|`docker images`|
|Remove an image|`docker rmi <image_name>`|
|Run a single task|`docker container run <image> <command>`|
|Show container logs|`docker container logs <container_name>`|
|See the container's running processes|`docker container top <container_name>`|
|Run a command [interactively] inside a container|`docker container exec [-it] <container_name> <command>`|
|Gracefully stop a container|`docker container stop <container_name>`|
|Docker Hub login|`docker login`|
|Push image to Docker Hub|`docker image push <image_name>`|

### *docker container run* command flags

|Action|Flag|
|--|--|
|Start an interactive session|`--interactive`|
|Allocate a pseudo-tty|`--tty`|
|Remove the container once it stops running|`--rm`|
|Run in the background|`--detach`|
|Give the container a name|`--name`|
|Create an environment variable|`-e`|
|Redirect traffic from host port to container port|`--publish <host_port>:<container_port>`|
|Mount directory into the container|`--mount`|

# Dockerfile instructions quick reference


|Instruction|Meaning|
|--|--|
|[CMD](https://docs.docker.com/engine/reference/builder/#cmd)|`CMD`  provides the defaults for an executing container. These defaults can include an executable. EXAMPLE: `CMD ["nginx", "-g", "daemon off;"]`|
|[COPY](https://docs.docker.com/engine/reference/builder/#copy)|Copies files or directories from the host's filesystem and adds them to the filesystem of the container. EXAMPLE: `COPY script.py /app/`|
|[ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint)|An `ENTRYPOINT` allows us to configure a container that will run as an executable. EXAMPLE: `ENTRYPOINT ["./script.py"]`|
|[FROM](https://docs.docker.com/engine/reference/builder/#from)|initializes a new build stage and sets the [_Base Image_](https://docs.docker.com/engine/reference/glossary/#base-image) for subsequent instructions. EXAMPLE: `FROM python:3`|
|[LABEL](https://docs.docker.com/engine/reference/builder/#label)|A `LABEL` is a key-value pair that adds metadata to an image. EXAMPLE: `LABEL maintainer="User <@user>"`|
|[RUN](https://docs.docker.com/engine/reference/builder/#run)|Executes commands in a new layer on top of the current image and commits the results, creating a committed image to be used for the next `Dockefile step`. EXAMPLE: `RUN pip install requests`|
|[WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir)|Sets the working directory for any `RUN`, `CMD`, `ENTRYPOINT`, `COPY` and `ADD` instructions that follow it in the `Dockerfile`. EXAMPLE: `WORKDIR /app`|

# References

[https://training.play-with-docker.com/](https://training.play-with-docker.com/)

[https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)
