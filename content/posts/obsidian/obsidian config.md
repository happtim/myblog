+++
date = '2023-06-19'
title = 'obsidian 配置'
+++


## 安装插件

### 图床（picgo）

PicGo是一款开源的图片上传、管理和分享工具。它可以帮助用户快速上传本地图片到云存储，并生成图片链接，方便在博客、社交媒体等地方分享图片。PicGo支持多种云存储平台，包括七牛云、腾讯云COS、阿里云OSS等，用户可以根据自己的需求选择合适的云存储方式。同时，PicGo还提供基本的图片编辑功能，如图片裁剪、旋转等。总之，PicGo是一款功能强大、简单易用的图片上传工具。

软件地址 https://picgo.github.io/PicGo-Doc

下载地址 [Releases · Molunerfinn/PicGo (github.com)](https://github.com/Molunerfinn/PicGo/releases)

下载文件版本为：PicGo-Setup-2.3.1-x64

如何下载比较慢，可以下载备份在网盘里面的 。 https://www.aliyundrive.com/s/wFhAkS9HUJb  目录为：软件-知识管理

安装完成后进行配置，这里使用腾讯云COS作为图床，配置如下截图。

![](https://assets.happtim.com/image/n3dc/20230620002549.png)

COS 购买的地址如下，一年10G的费用大概是在10元左右。
https://curl.qcloud.com/WtP18Ftw


### 图床插件（image auto upload）

有了图床软件之后，他会程序启动自带一个网络服务，可以为第三方插件使用。该软件可以将黏贴在Obsidian中的图片上传到picgo，并且返回markdown格式文件。

![image.png](https://assets.happtim.com/image/n3dc/202306200029069.png)


### 同步（github）

前提：已经安装git

obsidian的文章同步使用github，需要安装git的插件。

![image.png](https://assets.happtim.com/image/n3dc/202306200032057.png)

修改文件后提交同步文件，

![image.png](https://assets.happtim.com/image/n3dc/202306200038650.png)

