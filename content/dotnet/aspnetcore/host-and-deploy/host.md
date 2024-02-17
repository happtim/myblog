## Windows中托管

### 下载nssm

下载地址： https://nssm.cc/download

### 安装

在nssm.exe同级目录中打开控制台，输入.\nssm.exe install

![image.png](https://assets.happtim.com/image/n3dc/202401301629794.png)

Path: 程序的路径
Startup directory：默认带出
Arguments：可执行程序参数

Service name：服务的名称

### 启动
![image.png](https://assets.happtim.com/image/n3dc/202401301632587.png)

在windows服务中，找到对应得服务，右键启动。

### 删除

./nssm.exe remove nginx


## Linux托管

**安装守护进程**

1.       在/etc/systemd/system 下新建文件文件wcs.service，文件需以.Service结尾

![](file:///C:/Users/gexia/AppData/Local/Temp/msohtmlclip1/01/clip_image002.gif)

内容为：

```
[Unit]
Description= WcsService    #服务描述，随便填就好

[Service]
WorkingDirectory=/home/Wcs    #工作目录，填你应用的绝对路径

ExecStart=/usr/bin/dotnet /home/Wcs/Wcs.dll    #启动：前半截是你dotnet的位置（一般都在这个位置），后半部分是你程序入口的dll，中间用空格隔开

Restart=always
RestartSec=25    #如果服务出现问题会在25秒后重启，数值可自己设置
SyslogIdentifier=WcsService    #设置日志标识，此行可以没有
User=root    #配置服务用户，越高越好
Environment=ASPNETCORE_ENVIRONMENT=Production

[Install]
WantedBy=multi-user.target
```

2.       保存文件，输入指令确认服务，指令：systemctl enable wcs.service
3.       重启服务，指令：systemctl start wcs.service
4.       停止服务  systemctl stop wcs.service
5.       查看服务状态，指令：systemctl status wcs

