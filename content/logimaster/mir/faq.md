### 小车数据不同步

需要将小车从fleet中删除，然后再添加进入fleet中。记得要勾选factory reset the robot before adding it to the fleet。

![image.png](https://assets.happtim.com/image/n3dc/202402011845163.png)


### Wise模块异常

有时候由于网络问题，wise模块连接异常，需要使用loop和try/catch来包裹一下Set/Wait命令。

![image.png](https://assets.happtim.com/image/n3dc/202404011110995.png)
