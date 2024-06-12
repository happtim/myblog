## Centos8

1. 下载安装包。
2. 使用命令安装` sudo rpm -i SunloginClient_15.2.0.63062_x86_64.rpm`

### 缺少依赖

如果缺少依赖提示 ‘libXScrnSaver-devel 被 sunloginclient-15.2.0.63062-1.x86_64 需要’。

查找所需所需要的包 `sudo yum search libXScrnSaver` 。

安装所需要的包 `sudo yum install libXScrnSaver-devel.x86_64 libXScrnSaver.x86_64`

### 源失效

安装 `libXScrnSaver` 的时候会发现安装失败。

大家都知道Centos8于2021年年底停止了服务，大家再在使用yum源安装时候，出现下面错误“错误：Failed to download metadata for repo ‘AppStream’: Cannot prepare internal mirrorlist: No URLs in mirrorlist”

1. 进入yum的repos目录 `cd /etc/yum.repos.d/`
2. 修改所有的CentOS文件内容

```
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*

sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
```

3. 更新yum源为阿里镜像

```
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo

yum clean all

yum makecache
```

## Ubuntu 20.04

1. 下载安装包
2. 使用命令安装 `sudo dpkg -i 文件名.deb`

### 缺少依赖

如果出现

![image.png](https://assets.happtim.com/image/n3dc/202405131648397.png)


缺少依赖包，使用命令sudo apt-get install -f -y即可解决修复破损的包依赖，并完成deb的安装。


### 不登陆账户无法远程

向日葵不支持Ubuntu的原始桌面显示管理器GDM，需要更换掉原始桌面显示管理器，换上LightDM，然后就可以了。

`sudo apt-get install lightdm`

![image.png](https://assets.happtim.com/image/n3dc/202405131652870.png)

下载时候，中间会令你进入选择，你选择lightdm，选择确定就好了。

或者大家也可以后面在终端输入 `sudo dpkg-reconfigure lightdm`，也可以进入此页面进行设置