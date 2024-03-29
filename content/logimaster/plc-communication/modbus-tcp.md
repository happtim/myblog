1996年施耐德公司推出**基于以太网TCP/IP的modbus协**议。

ModbusTcp数据帧

Modbus协议定义了PDU（Protocol Data Unit），独立于其他通信层。在Modbus协议通过TCP/IP进行封装时，引入了一个额外的字段（MBAP头）。

![image.png](https://assets.happtim.com/image/n3dc/202402172214973.png)

MBAP的协议如下：

![image.png](https://assets.happtim.com/image/n3dc/202402172314600.png)

PDU由功能码+数据组成。功能码为1字节，数据长度不定。

modbus的操作对象

Modbus功能通过操作内存寄存器来监控和控制网络上的设备。Modbus设备的制造商通常会发布一个寄存器映射表。在尝试与设备通信之前，您应该参考特定设备的寄存器映射表，以了解其操作。

Modbus数据模型结构由四种基本数据类型组成：
-  离散输入 - 0xxxx
- 线圈（输出）- 1xxxx
- 输入寄存器（输入数据）- 3xxxx
- 保持寄存器（输出数据）- 4xxxx

 线圈: 是指在从站设备中表示开关状态的数据。每个线圈都只能存储一个位（即开关状态），可以是开（1）或关（0）。线圈的状态可以由主站进行读取或控制。读取线圈的功能码是0x01（读取线圈状态），写入单个线圈的功能码是0x05，写入多个线圈的功能码是0x0F。
 
 离散输入: 是指从站设备中输入的离散状态数据，比如传感器输出的开关状态。离散输入的每个位（开关状态）都可以被主站读取，但主站无法对离散输入的状态进行控制。读取离散输入的功能码是0x02。

根据对象的不同，modbus的功能码有：

**0x01**：读线圈

用于从从站设备中读取线圈（开关）的状态信息。请求中需要指定线圈的起始地址和数量。

请求：MBAP 功能码 起始地址 数量（共12字节）
响应：MBAP 功能码 数据长度 数据（一个地址的数据为1位）

示例：读取从站ID为1的设备中，地址为1000开始的10个线圈的状态。 
Modbus TCP请求：(00 01) (00 00) (00 06) (01) (01) (03 E8) (00 0A) 
Modbus TCP响应：00 01 00 00 00 05 01 01 02 55 AA

**0x02**：读离散量输入

用于从从站设备中读取离散输入端口的状态信息，如传感器输出的开关状态。请求中需要指定离散输入的起始地址和数量。

请求：MBAP 功能码 起始地址 数量（共12字节）  
响应：MBAP 功能码 数据长度 数据（长度：9+ceil（数量/8））

示例：读取从站ID为1的设备中，地址为5000开始的8个离散输入的状态。 
Modbus TCP请求：(00 01) (00 00) (00 06) (01) (02) (13 88) (00 08) 
Modbus TCP响应：(00 01) (00 00) (00 04) (01) (02) (01) (3F)

**0x05**：写单个线圈

该功能码用于向从站设备中写入单个线圈的状态。请求中需要指定线圈的地址和要写入的状态（1或0）。0xFF00请求输出为ON,0x000请求输出为OFF

请求：MBAP 功能码 输出地址 输出值（共12字节）  
响应：MBAP 功能码 输出地址 输出值（共12字节）

示例：向从站ID为1的设备中，地址为100的线圈写入状态1。 
Modbus TCP请求：(00 01) (00 00) (00 06) (01) (05) (00 64) (FF 00) 
Modbus TCP响应：00 01 00 00 00 06 01 05 00 64 FF 00

**0x0F**：写多个线圈

该功能码用于向从站设备中写入多个线圈的状态。请求中需要指定线圈的起始地址、数量和要写入的状态。数据域中置1的位请求相应输出位ON，置0的位请求响应输出为OFF

请求：MBAP 功能码 起始地址 输出数量 字节长度 输出值
响应：MBAP 功能码 起始地址 输出数量

示例：向从站ID为1的设备中，地址为500的起始的4个线圈写入状态为0110。 
Modbus TCP请求：(00 01) (00 00) (00 09) (01) (0F) (01 F4) (00 04) (01) (06) 
Modbus TCP响应：00 01 00 00 00 06 01 0F 01 F4 00 04


**0x03**：读保持寄存器

 用于从从站设备中读取保持寄存器的数据，比如传感器的测量值、控制器的参数等。请求中需要指定保持寄存器的起始地址和数量。
 
请求：MBAP 功能码 起始地址 寄存器数量（共12字节）  
响应：MBAP 功能码 数据长度 寄存器数据(长度：9+寄存器数量×2)

示例：从从站ID为1的设备中，读取地址为1000开始的5个Holding寄存器的值。
Modbus TCP请求：(00 01) (00 00) (00 06) (01) (03) (03 E8) (00 05) 
Modbus TCP响应：(00 01) (00 00) (00 0B) (01) (03) (0A 00) 01 02 03 04 05

**0x04**：读输入寄存器

用于从从站设备中读取输入寄存器的数据，比如模拟输入信号的测量值。请求中需要指定输入寄存器的起始地址和数量。

请求：MBAP 功能码 起始地址 寄存器数量（共12字节）  
响应：MBAP 功能码 数据长度 寄存器数据(长度：9+寄存器数量×2)

示例：从从站ID为1的设备中，读取地址为2000开始的3个Input寄存器的值。 
Modbus TCP请求：(00 01) (00 00) (00 06) (01) (04) (07 D0) (00 03） 
Modbus TCP响应：(00 01) (00 00) (00 07) (01) (04) (06 00) 01 02 03

**0x06**：写单个保持寄存器

 用于向从站设备中写入单个保持寄存器的值。请求中需要指定保持寄存器的地址和要写入的值。
 
请求：MBAP 功能码 寄存器地址 寄存器值（共12字节）  
响应：MBAP 功能码 寄存器地址 寄存器值（共12字节）

示例：向从站ID为1的设备中，地址为300的寄存器写入值0x0A。 
Modbus TCP请求：(00 01) (00 00) (00 06) (01) (06) (01 2C) (00 0A) 
Modbus TCP响应：00 01 00 00 00 06 01 06 01 2C 00 0A

**0x10**：写多个保持寄存器

 用于向从站设备中写入多个保持寄存器的值。请求中需要指定保持寄存器的起始地址、数量以及要写入的值。
 
请求：MBAP 功能码 起始地址 寄存器数量 字节长度 寄存器值（13+寄存器数量×2）  
响应：MBAP 功能码 起始地址 寄存器数量（共12字节）

示例：向从站ID为1的设备中，地址为400的寄存器写入3个值：0x0101, 0x0202, 0x0303。 
Modbus TCP请求：(00 01) (00 00) (00 0C) (01) (10) (01 90) (00 03) (06) (01 01) (02 02) (03 03) 
Modbus TCP响应：00 01 00 00 00 06 01 10 01 90 00 03