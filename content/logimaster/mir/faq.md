### 小车数据不同步

需要将小车从fleet中删除，然后再添加进入fleet中。记得要勾选factory reset the robot before adding it to the fleet。

![image.png](https://assets.happtim.com/image/n3dc/202402011845163.png)


### Wise模块异常

有时候由于网络问题，wise模块连接异常，需要使用loop和try/catch来包裹一下Set/Wait命令。

![image.png](https://assets.happtim.com/image/n3dc/202404011110995.png)

### Fleet Ros连接异常

日志显示 
/mir_persistence/var/log/mir/ros/9882b93c-fe8b-11ef-ae2b-0242ac120008

[rosout][ERROR] 2025-03-29 12:02:43,807: [Client 3135] [id: call_service:/get_log:12] call_service: can't start new thread
[rosout][ERROR] 2025-03-29 12:02:43,808: [Client 3135] [id: call_service:/get_log:13] call_service: can't start new thread
[rosout][ERROR] 2025-03-29 12:02:57,566: [Client 3135] [id: subscribe:/mir_log:11] unsubscribe: u'/mir_log'
[rosout][ERROR] 2025-03-29 12:02:58,322: [Client 3135] [id: subscribe:/mir_log:14] subscribe: can't start new thread
[rosout][ERROR] 2025-03-29 12:02:58,323: [Client 3135] [id: call_service:/get_log:15] call_service: can't start new thread
[rosout][ERROR] 2025-03-29 12:02:58,324: [Client 3135] [id: call_service:/get_log:16] call_service: can't start new thread
[rosout][ERROR] 2025-03-29 12:10:46,490: Unable to accept incoming connection.  Reason: can't start new thread
[rosout][INFO] 2025-03-29 12:10:46,491: Client connected.  -156 clients total.
[rosout][ERROR] 2025-03-29 12:11:33,130: Unable to accept incoming connection.  Reason: can't start new thread
[rosout][INFO] 2025-03-29 12:11:33,131: Client connected.  -157 clients total.
[rosout][ERROR] 2025-03-29 12:38:10,308: [Client 3135] [id: subscribe:/mir_log:14] unsubscribe: u'/mir_log'

### 重装Fleet

删除
sudo docker rm -f $(sudo docker ps -aq)
sudo docker system prune -af

安装
sudo chmod 777 *
sudo bash fleet-install-linux