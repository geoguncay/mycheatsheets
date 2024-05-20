# Docker Cheatsheet

Quick reference guide for Docker commands

### Table of Contents

| No. | Questions                                                       |
| --- | --------------------------------------------------------------- |
| 1   | [**Registries and Repositories**](#registries-and-repositories) |
| 2   | [**Create,Run,Update and Delete containers**](#)                |
| 3   | [**Start and stop containers**](#start-and-stop-containers)     |
| 4   | [**Networks**](#networks)                                       |
| 5   | [**Cleanup commands**](#cleanup-commands)                       |
| 6   | [**Utility commands**](#utility-commands)                       |
| 7   | [**Docker Hub**](#docker-hub)                                   |
| 8   | [**Dockerfile**](#dockerfile)                                   |
| 9   | [**Docker Compose**](#docker-compose)                           |
| 10  | [**Docker Swarm**](#docker-swarm)                               |

#### Repository:

A Docker Repository is a collection of related images with same name which have different tags. These tags are an alphanumeric identifiers(like 1.0 or latest) attached to images within a repository.

For example, if you want to pull python image using `docker pull python:latest` command, it will download the image tagged latest within the `python` repository from the Docker Hub registry. The tags appeared on dockerhub as below,

#### Login

Login to a registry

```cmd
docker login [OPTIONS] [SERVER]
```

[OPTIONS]:
-u/--username username
-p/--password password

Example:

````cmd
docker login localhost:8080
docker login
````

#### Logout

Logout from a registry

```cmd
docker logout [SERVER]
```

Example:

```cmd
docker logout localhost:8080 // Logout from a registry on your localhost
```

#### Search image

Search for an image in registry

Example:

```cmd
docker search [OPTIONS] TERM
docker search python
docker search --filter stars=3 --no-trunc python
```

#### Pull image

This command pulls an image or a repository from a registry to local machine

Example:

```cmd
docker image pull [OPTIONS] NAME[:TAG|@DIGEST]
docker image pull python:latest
```

#### Push image

This command pushes an image to the registry from local machine.

```cmd
docker image push [OPTIONS] NAME[:TAG]
docker image push python:latest
```

**[⬆ Back to Top](#table-of-contents)**

### Create,Run,Update and Delete containers

#### Create

Create a new container

Example:

```cmd
docker container create [OPTIONS] IMAGE [COMMAND] [ARG...]
docker container create -t -i py/python --name python
```

#### Rename

Rename a container

Example:

```cmd
docker container rename CONTAINER NEW_NAME
docker container rename python pythonuage
docker container rename pythonuage python
```

#### Run

```cmd
docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

Example:

```cmd
docker container run -it --name python -d sudheerj/python
```

You can also run a command inside container

```cmd
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

Example:

```cmd
docker exec -it python sh // Or use bash command if sh is failed
```

#### Update

Update configuration of one or more containers

```cmd
docker container update [OPTIONS] CONTAINER [CONTAINER...]
```

Example:

```cmd

docker container update --memory "1g" --cpuset-cpu "1" python // update the python to use 1g of memory and only use cpu core 1
```

#### Remove

Remove one or more containers

```cmd
docker container rm [OPTIONS] CONTAINER [CONTAINER...]
```

Example:

```cmd
docker container rm python
docker rm $(docker ps -q -f status=exited) // Remove all the stopped containers
```

**[⬆ Back to Top](#table-of-contents)**

### Start and stop containers

#### Start

Start one or more stopped containers

```cmd
docker container start [OPTIONS] CONTAINER [CONTAINER...]
```

Example:

```cmd
docker container start python
```

### Stop

Stop one or more running containers

```cmd
docker container stop [OPTIONS] CONTAINER [CONTAINER...]
```

Example:

```cmd
docker container stop python
docker stop $(docker ps -a -q) // To stop all the containers
```

#### Restart

Restart one or more containers and processes running inside the container/containers.

```cmd
docker container restart [OPTIONS] CONTAINER [CONTAINER...]

```

Example:

```cmd
docker container restart python
```

#### Pause

Pause all processes within one or more containers

```cmd
docker container pause CONTAINER [CONTAINER...]

```

Example:

```cmd
docker container pause python
```

### Unpause/Resume

Unpause all processes within one or more containers

```cmd
docker container unpause CONTAINER [CONTAINER...]
```

Example:

```cmd
docker container unpause python
```

#### Kill

Kill one or more running containers

```cmd
docker container kill [OPTIONS] CONTAINER [CONTAINER...]
```

Example:

```cmd
docker container kill python
```

#### Wait

Block until one or more containers stop and print their exit codes after that

```cmd
docker container wait CONTAINER [CONTAINER...]
```

Example:

```cmd
docker container wait python
```

**[⬆ Back to Top](#table-of-contents)**

### Networks

Docker provides network commands connect containers to each other and to other non-Docker workloads. The usage of network commands would be `docker network COMMAND`

#### List networks

List down available networks

```cmd
docker network ls
```

#### Connect a container to network

You can connect a container by name or by ID to any network. Once it connected, the container can communicate with other containers in the same network.

```cmd
docker network connect [OPTIONS] NETWORK CONTAINER
```

Example:

```cmd
docker network connect multi-host-network container1
```

#### Disconnect a container from a network

You can disconnect a container by name or by ID from any network.

```cmd
docker network disconnect [OPTIONS] NETWORK CONTAINER
```

Example:

```cmd
docker network disconnect multi-host-network container1
```

#### Remove one or more networks

Removes one or more networks by name or identifier. Remember, you must first disconnect any containers connected to it before removing it.

```cmd
docker network rm NETWORK [NETWORK...]
```

Example:

```cmd
docker network rm my-network
```

#### Create network

It is possible to create a network in Docker before launching containers

```cmd
docker network create [OPTIONS] NETWORK
```

Example:

```cmd
sudo docker network create –-driver bridge some_network
```

The above command will output the long ID for the new network.

#### Inspect network

You can see more details on the network associated with Docker using network inspect command.

Example:

```cmd
docker network inspect networkname
docker network inspect bridge
```

#### Disconnect network

You can disconnect docker from network

Example:

```cmd
docker network disconnect networkname
docker network disconnect bridge
```

#### Remove all unused resources

You may need to cleanup resources (containers, volumes, images, networks) regularly.

```cmd
docker network prune
```

```cmd
docker system prune
```

#### Images

```cmd
docker images
docker rmi $(docker images --filter "dangling=true" -q --no-trunc)

docker images | grep "none"
docker rmi $(docker images | grep "none" | awk '/ / { print $3 }')
```

#### Containers

```cmd
docker ps
docker ps -a
docker rm $(docker ps -qa --no-trunc --filter "status=exited")
```

#### Volumes

```cmd
docker volume rm $(docker volume ls -qf dangling=true)
docker volume ls -qf dangling=true | xargs -r docker volume rm
```

**[⬆ Back to Top](#table-of-contents)**

### Utility commands

**[⬆ Back to Top](#table-of-contents)**

### Docker Hub

Docker Hub is a cloud-based repository provided by Docker to test, store and distribute container images which can be accessed either privately or publicly.

#### From

It initializes a new image and sets the Base Image for subsequent instructions. It must be a first non-comment instruction in the Dockerfile.

```cmd
FROM <image>
FROM <image>:<tag>
FROM <image>@<digest>
```

**Note:** Both `tag` and `digest` are optional. If you omit either of them, the builder assumes a latest by default.

### Dockerfile

Dockerfile is a text document that contains set of commands and instructions which will be executed in a sequence in the docker environment for building a new docker image.

#### FROM

This command Sets the Base Image for subsequent instructions

```cmd
FROM <image>
FROM <image>:<tag>
FROM <image>@<digest>
```

Example:

```cmd
FROM ubuntu:18.04
```

#### RUN

RUN instruction allows you to install your application and packages required for it. It executes any commands on top of the current image and creates a new layer by committing the results. It is quite common to have multiple RUN instructions in a Dockerfile.

It has two forms

1. Shell Form: RUN <command>

```cmd
RUN npm start
```

2. Exec form RUN ["<executable>", "<param1>", "<param2>"]

```cmd
RUN [ "npm", "start" ]
```

#### ENTRYPOINT

An ENTRYPOINT allows you to configure a container that will run as an executable. It is used to run when container starts.

```cmd
Exec Form:
ENTRYPOINT ["executable", "param1", "param2"]
Shell Form:
ENTRYPOINT command param1 param2
```

Example:

```cmd
FROM alpine:3.5
ENTRYPOINT ["/bin/echo", "Print ENTRYPOINT instruction of Exec Form"]
```

If an image has an ENTRYPOINT and pass an argument to it while running the container, it wont override the existing entrypoint but it just appends what you passed with the entrypoint. To override the existing ENTRYPOINT. you should user `–entrypoint` flag for the running container.

Let's see the behavior with the above dockerfile,

```cmd
Build image:
docker build -t entrypointImage .

Run the image:
docker container run entrypointImage // Print ENTRYPOINT instruction of Exec Form

Override entrypoint:
docker run --entrypoint "/bin/echo" entrypointImage "Override ENTRYPOINT instruction" // Override ENTRYPOINT instruction
```

#### CMD

CMD instruction is used to set a default command, which will be executed only when you run a container without specifying a command. But if the docker container runs with a command, the default command will be ignored.

The CMD instruction has three forms,

1.

```cmd
Exec form:
CMD ["executable","param1","param2"]
```

2.

```cmd
Default params to ENTRYPOINT:
CMD ["param1","param2"]
```

3.

```cmd
Shell form:
CMD command param1 param2
```

The main purpose of the CMD command is to launch the required software in a container. For example, running an executable .exe file or a Bash terminal as soon as the container starts.

Remember, if docker runs with executable and parameters then CMD instruction will be overridden(Unlike ENTRYPOINT).

```cmd
docker run executable parameters
```

**Note:** There should only be one CMD command in your Dockerfile. Otherwise only the last instance of CMD will be executed.

#### COPY

The COPY instruction copies new files or directories from source and adds them to the destination filesystem of the container.

```cmd
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

Example:

```cmd
COPY test.txt /absoluteDir/
COPY tes? /absoluteDir/ // Copies all files or directories starting with test to destination container
```

The <src> path must be relative to the source directory that is being built. Whereas <dest> is an absolute path, or a path relative to `WORKDIR`.

#### ADD

The ADD instruction copies new files, directories or remote file URLs from source and adds them to the filesystem of the image at the destination path. The functionality is similar to COPY command and supports two forms of usage,

```cmd
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

Example:

```cmd
ADD test.txt /absoluteDir/
ADD tes? /absoluteDir/ // Copies all files or directories starting with test to destination container
```

ADD commands provides additional features such as downloading remote resources, extracting TAR files etc.

1. Download an external file and copy to the destination

```cmd
ADD http://source.file/url  /destination/path
```

2. Copies compressed files and extract the content in the destination

```cmd
ADD source.file.tar.gz /temp
```

#### ENV

The ENV instruction sets the environment variable <key> to the value <value>. It has two forms,

1. The first form, `ENV <key> <value>`, will set a single variable to a value.
2. The second form, `ENV <key>=<value> ...`, allows for multiple variables to be set at one time.

```cmd
ENV <key> <value>
ENV <key>=<value> [<key>=<value> ...]
```

Example:

```cmd
ENV name="John Doe" age=40
ENV name John Doe
ENV age 40
```

#### EXPOSE

The EXPOSE instruction informs Docker that the container listens on the specified network ports at runtime. i.e, It helps in inter-container communication. You can specify whether the port listens on TCP or UDP, and the default is TCP.

```cmd
EXPOSE <port> [<port>/<protocol>...]
```

Example:

```cmd
EXPOSE 80/udp
EXPOSE 80/tcp
```

But if you want to bind the port of the container with the host machine on which the container is running, use -p option of `docker run` command.

```cmd
docker run -p <HOST_PORT>:<CONTAINER:PORT> IMAGE_NAME
```

Example:

```cmd
docker run -p 80:80/udp myDocker
```

#### WORKDIR

The WORKDIR command is used to define the working directory of a Docker container at any given time for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow it in the Dockerfile.

```cmd
WORKDIR /path/to/workdir
```

Example:

```cmd
WORKDIR /c
WORKDIR d
WORKDIR e
RUN pwd  // /c/d/e
```

#### LABEL

The LABEL instruction adds metadata as key-value pairs to an image. Labels included in base or parent images (images in the FROM line) are inherited by your image.

```cmd
LABEL <key>=<value> <key>=<value> <key>=<value> ...
```

Example:

```cmd
LABEL version="1.0"
LABEL multi.label1="value1" \
      multi.label2="value2" \
      other="value3"
```

You can view an image’s labels using the `docker image inspect --format='' myimage` command. The output would be as below,

```cmd
{
  "version": "1.0",
  "multi.label1": "value1",
  "multi.label2": "value2",
  "other": "value3"
}
```

#### MAINTAINER

The MAINTAINER instruction sets the Author field of the generated images.

```cmd
MAINTAINER <name>
```

Example:

```cmd  
MAINTAINER John
```

This command is deprecated status now and the recommended usage is with LABEL command

```cmd
LABEL maintainer="John"
```

#### VOLUME

The VOLUME instruction creates a mount point with the specified name and mounted volumes from native host or other containers.

```cmd
VOLUME ["/data"]
```

Example:

```cmd
FROM ubuntu
RUN mkdir /test
VOLUME /test
```

### Docker Compose

Docker compose(or compose) is a tool for defining and running multi-container Docker applications.

### Docker Swarm

Docker Swarm(or swarm) is an open-source tool used to cluster and orchestrate Docker containers.
