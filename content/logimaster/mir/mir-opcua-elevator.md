
在linqpad中创建了一个Mir电梯的OPCUA服务。

主要工作量在电梯节点中操作。 在加载预定义的Nodes的时候，从文件中读取Mir给的xml节点描述文件。

然后再对数据进行初始化。

AddBehaviourToPredefinedNode 函数中， 要对函数绑定输入输出参数。虽然导入的节点数据中有输入输出参数。 但是在MethodState中不能使用，需要自己再根据文档绑定输入输出。

在变量发生变化时，要是的订阅的客户端得到通知。需要在变量的变化后ClearChangeMasks方法通知客户端。


### 客户端

下载Ua Expert。我们使用匿名的配置。

![image.png](https://assets.happtim.com/image/n3dc/202407061644838.png)

第一次连接的时候会提示证书确认。

![image.png](https://assets.happtim.com/image/n3dc/202407061644384.png)
