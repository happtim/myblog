+++
date = "2023-09-20"
title = "Wsl 安装Centos Stream 9并运行docker"
+++

## 下载CentOS WSL

github 上有人维护，在releases中找到 Centos 9-stream-yyyymmdd 下载
[mishamosher/CentOS-WSL: A GitHub Actions automated CentOS RootFS to use with WSL](https://github.com/mishamosher/CentOS-WSL)

## 解压文件并安装

![image.png](https://assets.happtim.com/image/n3dc/202309201701692.png)

解压后，您将在目标目录中看到2个文件：rootfs.tar.gz和CentOS.exe。我们需要运行**CentOS.exe**，以便解压其中的文件并注册到**WSL**。右键点击并以管理员身份运行

![image.png](https://assets.happtim.com/image/n3dc/202309201717685.png)


## 运行Centos

查询已经安装分发版本

```
wsl -l -v

  NAME                   STATE           VERSION
* docker-desktop         Running         2
  docker-desktop-data    Running         2
  CentOS9-stream         Stopped         2
```

运行指定分发

```
wsl -d CentOS9-stream
```

结束指定分发

```
 wsl -t CentOS9-stream
```


## 卸载Centos

```
.\CentOS9-stream.exe clean
This will remove this distro (CentOS9-stream) from the filesystem.
Are you sure you would like to proceed? (This cannot be undone)
Type "y" to continue:y
Unregistering...
```

## Docker 容器概述

Docker 是一种工具，用于创建、部署和运行应用程序（通过使用容器）。 容器使开发人员可以将应用与需要的所有部件（库、框架、依赖项等）打包为一个包一起交付。 使用容器可确保此应用的运行与之前相同，而不受任何自定义设置或运行该应用的计算机上先前安装的库的影响（运行应用的计算机可能与用于编写和测试应用代码的计算机不同）。 这使开发人员可以专注于编写代码，而无需操心将运行代码的系统。

Docker 容器与虚拟机类似，但不会创建整个虚拟操作系统。 相反，Docker 允许应用使用与运行它的系统相同的 Linux 内核。 这使得应用包能够仅要求主计算机上尚未安装的部件，从而降低包大小以及提高性能。

WSL 可以在 WSL 版本 1 或 WSL 2 模式下运行发行版。 可通过打开 PowerShell 并输入以下内容进行检查：`wsl -l -v`。 通过输入 `wsl --set-version <distro> 2`，确保发行版设置为使用 WSL 2。 将 `<distro>` 替换为发行版名称（例如 Ubuntu 18.04）。

在 WSL 版本 1 中，由于 Windows 和 Linux 之间的根本差异，Docker 引擎无法直接在 WSL 内运行，因此 Docker 团队使用 Hyper-V VM 和 LinuxKit 开发了一个替代解决方案。 但是，由于 WSL 2 现在在具有完整系统调用容量的 Linux 内核上运行，因此 Docker 可以在 WSL 2 中完全运行。 这意味着 Linux 容器可以在没有模拟的情况下以本机方式运行，从而在 Windows 和 Linux 工具之间实现更好的性能和互操作性。

## Docker配置

确保在“设置”>“常规”中选中“使用基于 WSL 2 的引擎”。

![image.png](https://assets.happtim.com/image/n3dc/202310240013829.png)

通过转到“设置”>“资源”>“WSL 集成”，从要启用 Docker 集成的已安装 WSL 2 发行版中进行选择。

![image.png](https://assets.happtim.com/image/n3dc/202310240013006.png)

若要确认已安装 Docker，请打开 WSL 发行版，并通过输入 `docker --version` 来显示版本和内部版本号

```shell
docker --version
Docker version 24.0.5, build ced0996
```

