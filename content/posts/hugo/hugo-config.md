+++
date = '2023-06-19'
title = "obsidian使用hugo搭建静态网页，github action上传文件到腾讯云cos "
+++

要使用Hugo搭建静态网页并将生成的文件上传到腾讯COS（对象存储），可以使用GitHub Actions实现自动化部署。


## hugo

Hugo是一个开源的静态网站生成框架。它是用Go语言编写的，旨在提供高效且快速的静态网站生成能力。Hugo的设计目标是通过简单易用的构建方式，快速生成高效、灵活、易于维护的静态网站。

### hugo安装

1. 在Hugo的官方网站上下载适用于您的操作系统的Hugo二进制文件。网址是：[Windows | Hugo (gohugo.io)](https://gohugo.io/installation/windows/) 
	1. 如果github下载慢，也可以我的网盘中下载。[阿里云盘分享 (aliyundrive.com)](https://www.aliyundrive.com/s/wFhAkS9HUJb) 目录地址：软件-知识管理
2. 下载对应的二进制之后，将他解压到我们Obsidian目录中。
3. 输入 "hugo version" 命令，检查是否成功安装Hugo，并查看Hugo的版本。
```
PS D:\Projects\Obsidian Vault> ./hugo.exe version
hugo v0.113.0-085c1b3d614e23d218ebf9daad909deaa2390c9a+extended windows/amd64 BuildDate=2023-06-05T15:04:51Z VendorInfo=gohugoio
```

### hugo初始化站点

```
hugo new site <site-name>
```

如我创建的为myblog，然后创建后的目录结构
1. `config.toml`：站点的主配置文件，用于定义全局的设置和参数。
2. `content`：存放网站的内容文件夹，通常包含多个 Markdown 文件，每个文件对应一个网页或文章。
3. `data`：存放数据文件夹，可以包含 JSON、YAML 或 CSV 格式的数据文件，用于在网站中使用动态数据。
4. `layouts`：存放布局模板文件夹，包含用于处理和渲染网站的不同部分的 HTML 模板文件。
5. `static`：存放静态文件和资源文件夹，如图片、CSS、JavaScript 等，这些文件会被直接复制到生成的网站中。
6. `themes`：存放主题文件夹，包含自定义的主题样式和布局文件，可以从外部导入主题或创建自定义主题。content
7. `archetypes`：存放模版文件夹，用于定义不同类型的内容文件（如文章、页面）的默认元数据和内容结构。
8. `archetypes/default.md`：默认的模版文件，用于创建新的内容文件时自动添加标题、日期等元数据。
9. `public`：生成的静态网站文件夹，包含最终生成的 HTML、CSS 和其他静态资源文件。

我们将obsidian的仓库指定在刚创建好的目录中，在content目录创建posts目录，将我我们原本的文件拷贝进去。

![image.png](http://assets.happtim.com/image/n3dc/202306201442177.png)


### hugo安装主题

我们选择主题为 [hugo-PaperMod](https://github.com/adityatelange/hugo-PaperMod)使用git submodule的方式安装。

```
# 第一次添加
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod

# 当重新克隆存储库时需要注意（子模块可能不会自动克隆）
git submodule update --init --recursive 
```

安装后配置主题,编辑文件 `hugo.toml`:

```toml
theme = "PaperMod"
```

### hugo 主题定制
**hugo-PaperMod** 主题主要有一个配置不满意，就是TOC在文章的上面，我们将他改造成浮动，希望他浮动到右侧。



### hugo网站启动

```
hugo server
```

程序默认会在http://localhost:1313/启动

![image.png](http://assets.happtim.com/image/n3dc/202306201444771.png)


## 编写Github Actions

1. 在 Hugo 项目的根目录创建一个名为 `.github/workflows` 的文件夹。
2. 在 `.github/workflows` 文件夹中创建一个名为 `hugo.yml` 的 YAML 文件，用于定义 GitHub Action 的工作流程。

在 `hugo.yml` 文件中，你可以使用以下代码作为模板：

``` yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Cos

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - main
      
jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    steps:
        - uses: actions/checkout@v3
          with:
            submodules: true  # Fetch Hugo themes (true OR recursive)
            fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

        - name: Setup Hugo
          uses: peaceiris/actions-hugo@v2
          with:
            hugo-version: '0.113.0'
            extended: true

        - name: Build
          run: hugo --minify
          
        - name: Deploy
          uses: TencentCloud/cos-action@v1
          with:
            secret_id: ${{ secrets.TENCENT_CLOUD_SECRET_ID }}
            secret_key: ${{ secrets.TENCENT_CLOUD_SECRET_KEY }}
            cos_bucket: ${{ secrets.COS_BUCKET }}
            cos_region: ${{ secrets.COS_REGION }}
            local_path: public
            remote_path: /
            clean: true
            #accelerate: true
```

3. 在腾讯云控制台上创建一个 COS 存储桶。
4. 在 GitHub 仓库的 "Settings" 页面中，点击 "Secrets and variables" - "Actions"，创建四个新的 Secrets，分别将 BucketName，Region， Secret ID 和 Secret Key 填入。
![image.png](http://assets.happtim.com/image/n3dc/202306201329792.png)

5. 每次push成功之后，会执行actions
![image.png](http://assets.happtim.com/image/n3dc/202306201448904.png)
