
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

如果您想要一个临时容器，`docker run --rm` 将在停止后移除容器。在运行mysql客户端的时候，可以使用该参数 `docker run -it --rm mysql:5.7 mysql -h172.17.0.2 -uroot -p`

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
|                                                                                   |                                                     |                              |

### Info

| 命令                                                                               | 说明                                                                           | 例子                            |
| -------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- | ----------------------------- |
| [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps)           | shows running containers.                                                    | `docker ps`                   |
| [`docker logs`](https://docs.docker.com/engine/reference/commandline/logs)       | gets logs from container. (Available for `json-file` and `journald` in 1.10) | `docker logs  -f mycontainer` |
| [`docker inspect`](https://docs.docker.com/engine/reference/commandline/inspect) | looks at all the info on a container (including IP address).                 | `docker inspect mycontainer`  |
| [`docker events`](https://docs.docker.com/engine/reference/commandline/events)   | gets events from container.                                                  | `docker events`               |
| [`docker port`](https://docs.docker.com/engine/reference/commandline/port)       | shows public facing port of container.                                       | `docker port mycontainer`     |
| [`docker top`](https://docs.docker.com/engine/reference/commandline/top)         | shows running processes in container.                                        | `docker top mycontainer`      |
| [`docker stats`](https://docs.docker.com/engine/reference/commandline/stats)     | shows containers' resource usage statistics.                                 | `docker stats`                |
| [`docker diff`](https://docs.docker.com/engine/reference/commandline/diff)       | shows changed files in the container's filesystem.                           | `docker diff mycontainer`     |
|                                                                                  |                                                                              |                               |
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
| [`docker build`](https://docs.docker.com/engine/reference/commandline/build)   | creates image from Dockerfile.                                                                        | `docker build -t myimage:0.0.1 .`                            |
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

安装 Docker 时，它会自动创建 3 个网络接口（桥接、主机、无网络）。

* bridge 

如果不指定，新创建的容器默认将连接到bridge网络。
默认情况下，新的容器会启动到桥接网络中，宿主机可以ping通容器ip，容器中也能ping通宿主机。容器之前只能通过ip地址相互访问，由于容器的ip会随着启动的顺序发生变化，因此不推荐使用ip访问。

`docker run -it --rm busybox` 可以使用该命令去进入一个busybox容器，该容器连接到默认网络中，可以使用ping命令测试其他容器，和宿主机的连通。


* host

容器与宿主主机共享网络，不需要映射端口即可通过宿主主机IP访问。主机模式网络可用于优化性能，在容器需要处理大量端口的情况下，它不需要网络地址转化（NAT），并且不会为每个端口创建“用户空间代理”。

* none

禁用容器中的所有网络，在启动容器时使用。

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

`docker network create my-net` 创建一个网络。
`docker network connect my-net innolight-blazor` 将创建的网络和容器相连接，

![image.png](https://assets.happtim.com/image/n3dc/202405111233226.png)

如果docker run 直接使用--network 之后，将不会添加默认的网络。

## Dockerfile

### Instructions

- [.dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)
- [FROM](https://docs.docker.com/engine/reference/builder/#from) Sets the Base Image for subsequent instructions.
- [MAINTAINER (deprecated - use LABEL instead)](https://docs.docker.com/engine/reference/builder/#maintainer-deprecated) Set the Author field of the generated images.
- [RUN](https://docs.docker.com/engine/reference/builder/#run) execute any commands in a new layer on top of the current image and commit the results.
- [CMD](https://docs.docker.com/engine/reference/builder/#cmd) provide defaults for an executing container.
- [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) 指定容器运行时监听的网络端口，它并不会公开端口，仅起到声明的作用，公开的端口需要容器运行时使用-p参数。
- [ENV](https://docs.docker.com/engine/reference/builder/#env) sets environment variable.
- [ADD](https://docs.docker.com/engine/reference/builder/#add) copies new files, directories or remote file to container. Invalidates caches. Avoid `ADD` and use `COPY` instead.
- [COPY](https://docs.docker.com/engine/reference/builder/#copy) 将宿主机的文件复制到容器内。
- [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) configures a container that will run as an executable.
- [VOLUME](https://docs.docker.com/engine/reference/builder/#volume) creates a mount point for externally mounted volumes or other containers.
- [USER](https://docs.docker.com/engine/reference/builder/#user) sets the user name for following RUN / CMD / ENTRYPOINT commands.
- [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) 相当于cd命令，进入工作目录。
- [ARG](https://docs.docker.com/engine/reference/builder/#arg) defines a build-time variable.
- [ONBUILD](https://docs.docker.com/engine/reference/builder/#onbuild) adds a trigger instruction when the image is used as the base for another build.
- [STOPSIGNAL](https://docs.docker.com/engine/reference/builder/#stopsignal) sets the system call signal that will be sent to the container to exit.
- [LABEL](https://docs.docker.com/config/labels-custom-metadata/) apply key/value metadata to your images, containers, or daemons.
- [SHELL](https://docs.docker.com/engine/reference/builder/#shell) override default shell is used by docker to run commands.
- [HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) tells docker how to test a container to check that it is still working.

### 多阶段构建
在基于.Net的应用程序时，需要一个SDK将源码编译为可执行程序，但是在生产中是不需要SDK的。
多阶段构建可以将生产时依赖与运行时依赖分开，减小整体image的文件大小。

```
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS build
COPY bin/Release/net7.0/publish/ app/
  
FROM nginx:alpine-slim AS final
WORKDIR /usr/share/nginx/html
COPY --from=build /app/wwwroot .
COPY /nginx.conf  /etc/nginx/conf.d/default.conf
```

1. **第一阶段：`build`**
    
    `FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS build COPY bin/Release/net7.0/publish/ app/`
    
    这个阶段基于 `mcr.microsoft.com/dotnet/aspnet:7.0` 镜像，将名字标记为 `build`。它主要用于将编译后的.NET应用程序从宿主机的 `bin/Release/net7.0/publish/` 目录复制到镜像中的 `app/` 目录。
    
2. **第二阶段：`final`**
    
    第二阶段基于 `nginx:alpine-slim` 镜像，将名字标记为 `final`。这个阶段设置工作目录为 `/usr/share/nginx/html`，这是Nginx默认的静态文件服务目录。
    
    - `COPY --from=build /app/wwwroot .` 这一行使用了 `--from=build` 参数。这意味着它从第一阶段名为 `build` 的镜像中的 `/app/wwwroot` 目录复制文件到当前工作目录（即 `/usr/share/nginx/html`）。这样做的目的是将.NET应用程序的静态内容（如HTML、CSS、JavaScript文件等）复制到可以通过Nginx服务的目录中。
    - `COPY /nginx.conf /etc/nginx/conf.d/default.conf` 这一行则是将宿主机上的 `nginx.conf` 文件复制到容器中的 `/etc/nginx/conf.d/default.conf`，用于配置Nginx服务器。`**

## 数据存储

将数据存储在容器中，一旦容器被删除，数据也会被删除。同时也会使容器变得越来越大，不方便恢复和迁移。
将数据存储到容器之外，这样删除容器也不会丢失数据。一旦容器故障，我们可以重新创建一个容器，将数据挂载到容易里，就可以快速的恢复。

### 存储方式

Docker 提供了以下存储选项

* volumn
卷存储在主机文件系统分配一块专有存储区域，由Docker(在Linux上)管理，并且与主机的核心功能隔离。非 Docker 进程不能修改文件系统的这一部分。卷是在 Docker 中持久保存数据的最佳方式。

数据卷的优势：
1. 卷可以在多个正在运行的容器之前共享数据，仅当显示删除卷时，才会删除卷。
2. 当你想将容器数据存储在外部网络存储上或者云提供商上，而不是本地。
3. 卷更容易备份或者迁移，当你需要备份，还原数据或者将数据从一个Docker主机前提到另一个Docker主机时，卷是更好的选择。

* bind mount 绑定挂载
绑定挂载可以将主机文件系统上目录或文件装载到容器中，但是主机上的非 Docker 进程可以修改它们，同时在容器中也可以更改主机文件系统，包括创建、修改或删除文件或目录，使用不当，可能会带来安全隐患。

绑定挂载适用场景：
1. 将配置文件从主机共享到容器
2. 在Docker主机上的开发环境和容器之前共享源代码或者编译目录。

* tmpfs 临时挂载
tmpfs挂载仅存储在主机系统的内存中，从不写入主机系统的文件系统。当容器停止时数据将被删除，

`docker run -d -it --tmpfs /tmp nginx:1.22-alpine`

### Bind Mount

`docker run --name some-mysql -v D:/mysql/config/mysql.cnf:/etc/mysql/conf.d/mysql.cnf:ro -v D:/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7`

使用该命令，创建一个mysql容器，并且指定配置文件D盘的mysq/config/mysql.cnf 和数据为D盘mysq/data

`docker exec some-mysql -it /bin/bash` 来确定下配置项。

### Volumes

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

## Docker Compose

### Basic Operations

|Command|Description|Example Command|
|---|---|---|
|[`docker-compose up`](https://docs.docker.com/compose/reference/up/)|Create and start containers according to the `docker-compose.yml`.|`docker-compose up`|
|[`docker-compose down`](https://docs.docker.com/compose/reference/down/)|Stop and remove containers, networks, images, and volumes.|`docker-compose down`|

### Service Management

|Command|Description|Example Command|
|---|---|---|
|[`docker-compose start`](https://docs.docker.com/compose/reference/start/)|Start existing containers.|`docker-compose start`|
|[`docker-compose stop`](https://docs.docker.com/compose/reference/stop/)|Stop running containers without removing them.|`docker-compose stop`|
|[`docker-compose restart`](https://docs.docker.com/compose/reference/restart/)|Restart services.|`docker-compose restart`|
|[`docker-compose pause`](https://docs.docker.com/compose/reference/pause/)|Pause services.|`docker-compose pause`|
|[`docker-compose unpause`](https://docs.docker.com/compose/reference/unpause/)|Unpause services.|`docker-compose unpause`|

### Configuration and Inspection

|Command|Description|Example Command|
|---|---|---|
|[`docker-compose config`](https://docs.docker.com/compose/reference/config/)|Validate and view the Compose file.|`docker-compose config`|
|[`docker-compose ps`](https://docs.docker.com/compose/reference/ps/)|List containers.|`docker-compose ps`|
|[`docker-compose logs`](https://docs.docker.com/compose/reference/logs/)|View output from containers.|`docker-compose logs`|
|[`docker-compose exec`](https://docs.docker.com/compose/reference/exec/)|Execute a command in a running container.|`docker-compose exec web bash`|
### compose文件结构
* `docker-compose.yml`通常需要包含以下几个顶级元素：
* `version` 已弃用，早期版本需要此元素。
* `services`必要元素，定义一个或多个容器的运行参数，在`services`中可以通过以下元素定义容器的运行参数
	* `image` 容器 镜像
	* `ports`端口映射
	* `environment`环境变量
	* `networks`容器使用的网络
	* `volumes`容器挂载的存储卷
	* `command`容器启动时执行的命令
	* `depends_on`定义启动顺序
	* 复数形式（例如`ports`,`networks`,`volumes`,`depends_on`）参数需要传入列表
* `networks`创建自定义网络
* `volumes` 创建存储卷

### Yaml 文件格式

- 缩进代表上下级关系
- 缩进时不允许使用Tab键，只允许使用空格
- `:` 键值对，后面必须有空格
- `-`列表，后面必须有空格
- `[ ]`数组
- `#`注释
- `{key:value,k1:v1}`map
- `|` 多行文本块

如果一个文件中包含多个文档

- `---`表示一个文档的开始

### healthcheck

`healthcheck`在Docker Compose中用于检查容器内服务的健康状态。它可以定期运行命令来验证服务是否正常运作。对于MySQL容器，`healthcheck`通常会检查MySQL服务是否已经开始在预期端口上监听请求。

通过在Docker Compose文件中定义`healthcheck`，你可以设定一系列条件和检测手段，Docker会根据这些条件自动检测服务状态。如果服务未按预期运行，`healthcheck`可以帮助快速发现问题。

### 数据库启动问题

数据库初始化完成之前，不会建立connections。

![image.png](https://assets.happtim.com/image/n3dc/202405111616864.png)
`depends_on`只能保证容器的启动和销毁顺序，不能确保依赖的容器是否ready。

要确保应用服务在数据库初始化完成后再启动，需要配合[condition](https://docs.docker.com/compose/compose-file/#depends_on)和[healthcheck](https://docs.docker.com/compose/compose-file/#healthcheck)使用。

`condition`有三种状态：
* `service_started`容器已启动
* `service_healthy`容器处于健康状态
* `service_completed_successfully`容器执行完成且成功退出（退出状态码为0）

### 应用程序启动问题

和mysql一样，应用程序启动启动并并不关系服务是否有异常，需要healthcheck去确定。

