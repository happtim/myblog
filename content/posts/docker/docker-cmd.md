
[wsargent/docker-cheat-sheet: Docker Cheat Sheet (github.com)](https://github.com/wsargent/docker-cheat-sheet?tab=readme-ov-file)

## 容器

### Lifecycle

| 命令                                                                              | 说明                                               | 例子                                            |
| ------------------------------------------------------------------------------- | ------------------------------------------------ | --------------------------------------------- |
| [`docker create`](https://docs.docker.com/engine/reference/commandline/create)  | creates a container but does not start it.       | docker create --name myubuntu ubuntu          |
| [`docker rename`](https://docs.docker.com/engine/reference/commandline/rename/) | allows the container to be renamed.              | `docker rename oldname newname`               |
| [`docker run`](https://docs.docker.com/engine/reference/commandline/run)        | creates and starts a container in one operation. | `docker run --name mynginx -p 80:80 -d nginx` |
| [`docker rm`](https://docs.docker.com/engine/reference/commandline/rm)          | deletes a container.                             | `docker rm mycontainer`                       |
| [`docker update`](https://docs.docker.com/engine/reference/commandline/update/) | updates a container's resource limits.           | `docker update --cpu-shares 512 mycontainer`  |

docker run: 通常，如果您不使用任何选项运行容器，它将立即启动并停止。如果您想保持其运行状态，可以使用命令`docker run -td container_id` -t 选项来分配一个伪终端会话，-d选项自动分离容器（在后台运行容器并打印容器 ID）。

如果您想要一个临时容器，`docker run --rm` 将在停止后移除容器。

如果您想将主机上的目录映射到 Docker 容器中 `docker run -v $HOSTDIR:$DOCKERDIR`

如果您想删除与容器关联的卷，删除容器必须包括参数-v， `docker rm -v`

在 Docker 1.10 中，还提供了针对单个容器的日志驱动程序。要使用自定义日志驱动程序（例如 syslog）运行 Docker 。使用命令 `docker run --log-driver=syslog`

另一个有用的选项是 --name，因为当您在运行命令中指定时名称，这将允许您通过使用创建容器时指定的名称来启动和停止容器。

### Starting and Stopping

| 命令                                                                                | 说明                                                  | 例子                           |
| --------------------------------------------------------------------------------- | --------------------------------------------------- | ---------------------------- |
| [`docker start`](https://docs.docker.com/engine/reference/commandline/start)      | starts a container so it is running.                | `docker start mycontainer`   |
| [`docker stop`](https://docs.docker.com/engine/reference/commandline/stop)        | stops a running container.                          | `docker stop mycontainer`    |
| [`docker restart`](https://docs.docker.com/engine/reference/commandline/restart)  | stops and starts a container.                       | `docker restart mycontainer` |
| [`docker pause`](https://docs.docker.com/engine/reference/commandline/pause/)     | pauses a running container, "freezing" it in place. | `docker pause mycontainer`   |
| [`docker unpause`](https://docs.docker.com/engine/reference/commandline/unpause/) | will unpause a running container.                   | `docker unpause mycontainer` |
| [`docker wait`](https://docs.docker.com/engine/reference/commandline/wait)        | blocks until running container stops.               | `docker wait mycontainer`    |
| [`docker kill`](https://docs.docker.com/engine/reference/commandline/kill)        | sends a SIGKILL to a running container.             | `docker kill mycontainer`    |
| [`docker attach`](https://docs.docker.com/engine/reference/commandline/attach)    | will connect to a running container.                | `docker attach mycontainer`  |

### Info

| 命令                                                                               | 说明                                                                           | 例子                           |
| -------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- | ---------------------------- |
| [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps)           | shows running containers.                                                    | `docker ps`                  |
| [`docker logs`](https://docs.docker.com/engine/reference/commandline/logs)       | gets logs from container. (Available for `json-file` and `journald` in 1.10) | `docker logs mycontainer`    |
| [`docker inspect`](https://docs.docker.com/engine/reference/commandline/inspect) | looks at all the info on a container (including IP address).                 | `docker inspect mycontainer` |
| [`docker events`](https://docs.docker.com/engine/reference/commandline/events)   | gets events from container.                                                  | `docker events`              |
| [`docker port`](https://docs.docker.com/engine/reference/commandline/port)       | shows public facing port of container.                                       | `docker port mycontainer`    |
| [`docker top`](https://docs.docker.com/engine/reference/commandline/top)         | shows running processes in container.                                        | `docker top mycontainer`     |
| [`docker stats`](https://docs.docker.com/engine/reference/commandline/stats)     | shows containers' resource usage statistics.                                 | `docker stats`               |
| [`docker diff`](https://docs.docker.com/engine/reference/commandline/diff)       | shows changed files in the container's filesystem.                           | `docker diff mycontainer`    |
|                                                                                  |                                                                              |                              |
`docker ps -a`显示正在运行和已停止的容器。

`docker stats --all` 显示所有容器的列表，默认只显示正在运行的。

### Import / Export

| 命令                                                                             | 说明                                                                    | 例子                                                         |
| ------------------------------------------------------------------------------ | --------------------------------------------------------------------- | ---------------------------------------------------------- |
| [`docker cp`](https://docs.docker.com/engine/reference/commandline/cp)         | copies files or folders between a container and the local filesystem. | `docker cp mycontainer:/path/to/file /local/path`          |
| [`docker export`](https://docs.docker.com/engine/reference/commandline/export) | turns container filesystem into a tarball archive stream to STDOUT.   | `docker export my_container \| gzip > my_container.tar.gz` |

### Executing Commands
| 命令                                                                         | 说明                                 | 例子                            |
| -------------------------------------------------------------------------- | ---------------------------------- | ----------------------------- |
| [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec) | to execute a command in container. | docker exec -it foo /bin/bash |

要进入一个正在运行的容器，将一个新的 shell 进程附加到名为 foo 的运行中的容器上，使用`docker exec -it foo /bin/bash`

## Image

### Lifecycle

| 命令                                                                             | 说明                                                                                                    | 例子                                                           |
| ------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| [`docker images`](https://docs.docker.com/engine/reference/commandline/images) | shows all images.                                                                                     | `docker images`                                              |
| [`docker import`](https://docs.docker.com/engine/reference/commandline/import) | creates an image from a tarball.                                                                      | `cat my_container.tar.gz \| docker import - my_image:my_tag` |
| [`docker build`](https://docs.docker.com/engine/reference/commandline/build)   | creates image from Dockerfile.                                                                        | `docker build -t myimage .`                                  |
| [`docker commit`](https://docs.docker.com/engine/reference/commandline/commit) | creates image from a container, pausing it temporarily if it is running.                              | `docker commit mycontainer mynewimage`                       |
| [`docker rmi`](https://docs.docker.com/engine/reference/commandline/rmi)       | removes an image.                                                                                     | `docker rmi myimage`                                         |
| docker image prune                                                             | 清除未使用的镜像                                                                                              |                                                              |
| [`docker load`](https://docs.docker.com/engine/reference/commandline/load)     | loads an image from a tar archive as STDIN, including images and tags (as of 0.7).                    | `docker load < my_image.tar.gz`                              |
| [`docker save`](https://docs.docker.com/engine/reference/commandline/save)     | saves an image to a tar archive stream to STDOUT with all parent layers, tags & versions (as of 0.7). | `docker save my_image:my_tag \| gzip > my_image.tar.gz`      |

export import 和 save load区别？
* export 导出丢失镜像所有的历史记录和元数据信息。
* save保存没有丢失镜像的历史，可以回滚到之前的层。
- docker export 的应用场景：主要用来制作基础镜像，比如我们从一个 ubuntu 镜像启动一个容器，然后安装一些软件和进行一些设置后，使用 docker export 保存为一个基础镜像。然后，把这个镜像分发给其他人使用，比如作为基础的开发环境。
- docker save 的应用场景：如果我们的应用是使用 docker-compose.yml 编排的多个镜像组合，但我们要部署的客户服务器并不能连外网。这时就可以使用 docker save 将用到的镜像打个包，然后拷贝到客户服务器上使用 docker load 载入。

### Info

| 命令                                                                               | 说明                                           | 例子                                            |
| -------------------------------------------------------------------------------- | -------------------------------------------- | --------------------------------------------- |
| [`docker history`](https://docs.docker.com/engine/reference/commandline/history) | shows history of an image.                   | `docker history myimage`                      |
| [`docker tag`](https://docs.docker.com/engine/reference/commandline/tag)         | tags an image to a name (local or registry). | `docker tag myimage myregistry/myimage:mytag` |

## NetWorks

安装 Docker 时，它会自动创建 3 个网络接口（桥接、主机、无网络）。默认情况下，新的容器会启动到桥接网络中。为了实现多个容器之间的通信，您可以创建一个新的网络并在其中启动容器。这样可以使容器彼此通信，同时与未连接到网络的容器隔离。此外，它还允许将容器名称映射到其 IP 地址。

### Lifecycle

|命令|说明|例子|
|---|---|---|
|[`docker network create`](https://docs.docker.com/engine/reference/commandline/network_create/)|Create a new network (default type: bridge).|`docker network create --driver bridge mynetwork`|
|[`docker network rm`](https://docs.docker.com/engine/reference/commandline/network_rm/)|Remove one or more networks by name or identifier.|`docker network rm mynetwork`|

### Info

|命令|说明|例子|
|---|---|---|
|[`docker network ls`](https://docs.docker.com/engine/reference/commandline/network_ls/)|List networks|`docker network ls`|
|[`docker network inspect`](https://docs.docker.com/engine/reference/commandline/network_inspect/)|Display detailed information on one or more networks.|`docker network inspect mynetwork`|

### Connection

| 命令                                                                                                      | 说明                                    | 例子                                                |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------- |
| [`docker network connect`](https://docs.docker.com/engine/reference/commandline/network_connect/)       | Connect a container to a network      | `docker network connect mynetwork mycontainer`    |
| [`docker network disconnect`](https://docs.docker.com/engine/reference/commandline/network_disconnect/) | Disconnect a container from a network | `docker network disconnect mynetwork mycontainer` |

## Dockerfile

### Instructions

- [.dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)
- [FROM](https://docs.docker.com/engine/reference/builder/#from) Sets the Base Image for subsequent instructions.
- [MAINTAINER (deprecated - use LABEL instead)](https://docs.docker.com/engine/reference/builder/#maintainer-deprecated) Set the Author field of the generated images.
- [RUN](https://docs.docker.com/engine/reference/builder/#run) execute any commands in a new layer on top of the current image and commit the results.
- [CMD](https://docs.docker.com/engine/reference/builder/#cmd) provide defaults for an executing container.
- [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) informs Docker that the container listens on the specified network ports at runtime. NOTE: does not actually make ports accessible.
- [ENV](https://docs.docker.com/engine/reference/builder/#env) sets environment variable.
- [ADD](https://docs.docker.com/engine/reference/builder/#add) copies new files, directories or remote file to container. Invalidates caches. Avoid `ADD` and use `COPY` instead.
- [COPY](https://docs.docker.com/engine/reference/builder/#copy) copies new files or directories to container. By default this copies as root regardless of the USER/WORKDIR settings. Use `--chown=<user>:<group>` to give ownership to another user/group. (Same for `ADD`.)
- [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) configures a container that will run as an executable.
- [VOLUME](https://docs.docker.com/engine/reference/builder/#volume) creates a mount point for externally mounted volumes or other containers.
- [USER](https://docs.docker.com/engine/reference/builder/#user) sets the user name for following RUN / CMD / ENTRYPOINT commands.
- [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) sets the working directory.
- [ARG](https://docs.docker.com/engine/reference/builder/#arg) defines a build-time variable.
- [ONBUILD](https://docs.docker.com/engine/reference/builder/#onbuild) adds a trigger instruction when the image is used as the base for another build.
- [STOPSIGNAL](https://docs.docker.com/engine/reference/builder/#stopsignal) sets the system call signal that will be sent to the container to exit.
- [LABEL](https://docs.docker.com/config/labels-custom-metadata/) apply key/value metadata to your images, containers, or daemons.
- [SHELL](https://docs.docker.com/engine/reference/builder/#shell) override default shell is used by docker to run commands.
- [HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) tells docker how to test a container to check that it is still working.


## Volumes

Docker 卷是自由浮动的文件系统。它们不必连接到特定的容器。您可以使用从数据容器挂载的卷来实现可移植性。从 Docker 1.9.0 开始，Docker 引入了命名卷，取代了数据容器。考虑使用命名卷来实现，而不是数据容器。

因为卷是隔离的文件系统，所以它们经常被用来存储临时容器之间的计算状态。也就是说，你可以从一个配方中运行一个无状态的临时容器，然后将其删除，然后再次创建一个临时容器的实例，从上一个容器停止的地方继续执行。

### Lifecycle

|Command|Description|Example Command|
|---|---|---|
|[`docker volume create`](https://docs.docker.com/engine/reference/commandline/volume_create/)|Create a new volume.|`docker volume create myvolume`|
|[`docker volume rm`](https://docs.docker.com/engine/reference/commandline/volume_rm/)|Remove one or more volumes.|`docker volume rm myvolume`|

### Info

| Command                                                                                         | Description                                          | Example Command                  |
| ----------------------------------------------------------------------------------------------- | ---------------------------------------------------- | -------------------------------- |
| [`docker volume ls`](https://docs.docker.com/engine/reference/commandline/volume_ls/)           | List all volumes.                                    | `docker volume ls`               |
| [`docker volume inspect`](https://docs.docker.com/engine/reference/commandline/volume_inspect/) | Display detailed information on one or more volumes. | `docker volume inspect myvolume` |
## Exposing ports

-p 参数使得容器端口映射到主机端口（仅使用本地主机接口）。

```
docker run -p 127.0.0.1:$HOSTPORT:$CONTAINERPORT \
  --name CONTAINER \
  -t someimage
```

您可以使用 EXPOSE 在运行时告诉 Docker 容器监听指定的网络端口

`EXPOSE <CONTAINERPORT>`

请注意，本身不会暴露端口 - 只有会这样做。