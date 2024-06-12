
引用文章：
https://getiot.tech/zh/linux-note/use-of-quectel-ec20-dialup-on-linux

## 准备工作

### 硬件连接

将4g模块插入工控机的mini PCIe接口中。

### 查看连接

EC20 模块封装成标准的 PCIe 接口，实际走的是 USB 信号，并虚拟出多个 ttyUSB 设备节点。

执行lsusb命令，如果出现如下内容则模块已经成功被 Linux 系统识别到。

```
Bus 002 Device 009: ID 2c7c:0125 Quectel Wireless Solutions Co., Ltd. EC25 LTE modem
```

其中，`0x2C7C` 和 `0x0125` 分别是 Quectel EC25/EC20 R2.0 的 VID 和 PID 编号。

### 查看驱动加载

查看`dmesg`信息，确认模块驱动加载情况。不同的4G/5G网卡，加载的驱动模式可能有差异。通常情况下，4G无线网卡包含两个模式，一个CD存储模式，另一个是Modem模式（调制解调器模式）。如果usb模式切换正常，通常可以看到类似如下输出：

```
ttyUSB1: GSM modem (1-port) converter now disconnected from ttyUSB1
```

否则，可能只识别到`USB Storage device`或者`CD-ROM`。这种情况下，需要安装`usb-modeswitch usb-modeswitch-data`等库，并设置切换为`modem`模式。

## AT功能

当连接模块并加载 USB 驱动成功后，在 /dev 目录下将会创建出几个 tty 设备节点。例如 /dev/ttyUSB2 是 AT 指令的控制端口。

```
$ ls -l /dev/ttyUSB*
crw-rw---- 1 root dialout 188, 0 6月  26 00:14 /dev/ttyUSB0
crw-rw---- 1 root dialout 188, 1 6月  26 00:13 /dev/ttyUSB1
crw-rw---- 1 root dialout 188, 2 6月  26 00:16 /dev/ttyUSB2
crw-rw---- 1 root dialout 188, 3 6月  26 00:14 /dev/ttyUSB3
```

其中 ttyUSB0 为模块的 DM 端口，ttyUSB1 为 GPS NMEA 数据输出端口，ttyUSB2 为 AT 指令通信端口，ttyUSB3 为 PPP 连接端口。

接下来，就可以通过 ttyUSB2 串口输入 AT 指令并接收返回数据。在 Linux 下最直接的方式就是使用 echo 和 cat 命令，分别打开两个终端，一边执行 echo 命令发送 AT 指令，一边执行 cat 命令等待数据。

```
cat /dev/ttyUSB2 & echo -e "ATI\r\n" > /dev/ttyUSB2
```

为了更好地交互，建议使用 [minicom](https://getiot.tech/zh/linux-command/minicom) 或 [microcom](https://getiot.tech/zh/linux-command/microcom) 这些串口调试工具进行测试。

执行 `AT+CPIN?` 命令查询 SIM 卡状态，如果返回 `+CPIN: READY` 表示 SIM 卡准备就绪，如果返回 `+CME ERROR: 10` 表示 SIM not inserted，如果返回 `+CME ERROR: 13` 表示 SIM failure。

可以通过下面 AT 指令来简单测试短信发送功能。

```
AT+CMGF=1
AT+CSCS="GSM"
AT+CMGS="your-phone-number"
> Hello World!
Ctrl-Z
```

在 `AT+CMGS` 后面的字串中填入接收信息的号码，收到 “>” 提示符后填入短信内容，按 Ctrl-Z 即可发送短信。


## 配置wvdial拨号

wvdial 是 Linux 下的智能化拨号工具，利用 wvdial 和 ppp 可以实现 Linux 下的轻松上网。在整个过程中 wvdial 的作用是拨号并等待提示，并根据提示输入相应的用户名和密码等认证信息；ppp 的作用是与拨入方协商传输数据的方法并维持该连接。

安装 `sudo apt install wvdial ppp`

wvdial 的功能很强大，会试探着去猜测如何拨号及登录到服务器，同时它还会对常见的错误智能的进行处理，不需要你去写登录脚本。wvdial 只有一个配置文件 /etc/wvdial.conf。

可以用 wvdialconf 程序自动生成 wvdial.conf 配置文件，运行该程序的格式为：`wvdialconf /etc/wvdial.conf`

在执行该程序的过程中，程序会自动检测你的modem的相关配置，包括可用的设备文件名，modem 的波特率，初始化字符等相关的拨号信息，并根据这些信息自动生成 wvdial.conf 配置文件。如果 /etc/wvdial.conf 文件已经存在时，再次执行该命令只会改变其中的 Modem、Band、Init 等选项。一个典型的自动生成的配置文件可能是这样的：

```
[Dialer Defaults]
Modem = /dev/ttyUSB0
Baud = 115200
Init1 = ATZ
Init2 = ATQ0 V1 E1 S0=0 &C1 &D2 S11=55 +FCLASS=0
; Phone =
; Username =
; Password =
```

生成 /etc/wvdial.conf 文件后，还需要根据您所使用的 SIM 卡运营商进行配置。我们可以添加一个 EC20 专用的 section，该 section 的相应选项配置将会覆盖 Defaults 的配置。

移动或电信卡修改内容如下：

```
[Dialer ec20]  
Init1 = ATZ  
Init2 = ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0  
Modem Type = Analog Modem  
Modem = /dev/ttyUSB3  
Baud = 115200  
New PPPD = yes  
ISDN = 0  
Phone = *99#  
Username = card  
Password = card
```


联通卡修改内容如下：

```
[Dialer ec20]  
Init1 = ATZ  
Init2 = ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0  
Init3 = at+cgdcont=1,"ip","uninet"  
Modem Type = Analog Modem  
Modem = /dev/ttyUSB3  
Baud = 115200  
New PPPD = yes  
ISDN = 0  
Phone = *99#  
Password = card  
Username = card
```

### wvdial 拨号

sudo wvdial ec20

连接成功

```
--> WvDial: Internet dialer version 1.61
--> Initializing modem.
--> Sending: ATZ
ATZ
OK
--> Sending: ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0
ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0
OK
--> Modem initialized.
--> Sending: ATDT*99#
--> Waiting for carrier.
ATDT*99#
CONNECT 150000000
--> Carrier detected.  Waiting for prompt.
--> Don't know what to do!  Starting pppd and hoping for the best.
--> Starting pppd at Sun Jun 27 22:38:16 2021
--> Pid of pppd: 271769
--> Using interface ppp0
--> local  IP address 10.197.68.69
--> remote IP address 10.64.64.64
--> primary   DNS address 221.179.38.7
--> secondary DNS address 120.196.165.7
```

## 测试

EC20 拨号成功后，可以看到 ppp0 网卡

```
$ ifconfig ppp0
ppp0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1500
        inet 10.116.222.105  netmask 255.255.255.255  destination 10.64.64.64
        ppp  txqueuelen 3  (点对点协议)
        RX packets 5  bytes 62 (62.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 38  bytes 5389 (5.3 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

指定 ppp0 网卡进行网络连通测试

```
$ ping -I ppp0 getiot.tech
PING getiot.tech (42.192.64.149) from 10.116.222.105 ppp0: 56(84) bytes of data.
64 比特，来自 42.192.64.149 (42.192.64.149): icmp_seq=1 ttl=54 时间=229 毫秒
64 比特，来自 42.192.64.149 (42.192.64.149): icmp_seq=2 ttl=54 时间=127 毫秒
64 比特，来自 42.192.64.149 (42.192.64.149): icmp_seq=3 ttl=54 时间=146 毫秒
64 比特，来自 42.192.64.149 (42.192.64.149): icmp_seq=4 ttl=54 时间=119 毫秒
```