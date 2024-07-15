
## 注意

三菱PLC 地址使用 八进制表示 Y10  = Y 8
## 直连模式

将电脑的网口和PLC直连，显示该电脑网口的ip：169.254.71.68
点击测试通信。

![image.png](https://assets.happtim.com/image/n3dc/202405081756791.png)

## 使用指定IP方式

设置PLC型号

![image.png](https://assets.happtim.com/image/n3dc/202405081800525.png)

设置PLC的ip地址为：169.254.71.100。

![image.png](https://assets.happtim.com/image/n3dc/202405081801895.png)

写入修改的ip地址，并重启PLC。

![image.png](https://assets.happtim.com/image/n3dc/202405081805087.png)

设置集线器连接，将写入PLC ip地址的填写。并测试连接。

![image.png](https://assets.happtim.com/image/n3dc/202405081807119.png)

可以用搜索的方式。
![image.png](https://assets.happtim.com/image/n3dc/202405081808260.png)

## 设置MC通信

对象设备连接配置设置

可以在自节点设置中，设置通信数据代码，可以是二进制，和ASCII方式。二进制可以发送消息更多而且传送更快，默认使用。

![image.png](https://assets.happtim.com/image/n3dc/202405081818933.png)

拖入一个SLMP连接设备，并设置端口号为6000，应用并写入PLC。

一个SLMP连接设备只能有一个客户端连接，如果需要多个客户端同时访问，需要增加新的SLMP连接设备。

![image.png](https://assets.happtim.com/image/n3dc/202405081819365.png)


## 测试

![image.png](https://assets.happtim.com/image/n3dc/202405090000501.png)
