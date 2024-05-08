
## Docker 简介

Docker是一个用于 构建（build），运行（Run），传送（Share）应用程序的平台。

## Docker 和 虚拟机区别？

资源利用率：传统虚拟机是通过在物理服务器上运行多个虚拟机来实现虚拟化，每个虚拟机都包含一个完整的操作系统和应用程序，因此会占用大量的资源。而 Docker 使用容器技术，多个容器可以共享同一个操作系统内核，因此相比虚拟机更加轻量级，资源利用率更高。

### Docker基本概念

![image.png](https://assets.happtim.com/image/n3dc/202405062330510.png)

1. Docker 镜像（Image）：Docker 镜像是一个只读的模板，包含了运行容器所需的文件系统和应用程序。可以通过 Dockerfile 来定义镜像的内容和构建过程。
2. Docker 容器（Container）：Docker 容器是 Docker 镜像的运行实例，包含了应用程序及其依赖项。容器是独立、隔离的运行环境，可以在任何支持 Docker 的环境中运行。
3. Docker 仓库（Repository）：Docker 仓库是用来存储和分享 Docker 镜像的地方，可以分为公共仓库（如 Docker Hub）和私有仓库。

### 容器化和Dockerfile

Dockerfile 是一个文本文件，包含了构建 Docker 镜像的指令和步骤。通过 Dockerfile 可以定义镜像的构建过程，包括基础镜像、依赖项安装、环境配置等。

1. 创建一个Dockerfile
2. 使用Dockerfile构建镜像
3. 使用镜像创建和运行容器。

### Docker Compose

Docker Compose 是 Docker 的编排工具，可以通过一个简单的 YAML 文件定义和管理多个容器的部署。可以使用 Docker Compose 来快速搭建多个服务之间的通信和协作。