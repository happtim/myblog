+++
Date = "2023-07-11"
Title = "IdentityServer实现Client Credential"

tags = ['oauth2.0','identityserver']
+++



### Postman测试

填写客户端信息，授权类型编写 `Client Credentials` 。 Client ID，Client Secret，Scope，按照注册客户端时候填写。Access Token URL 填写 为https://localhost:5001/connect/token。

![image.png](https://assets.happtim.com/image/n3dc/202307111705003.png)

获取Access Token

![image.png](https://assets.happtim.com/image/n3dc/202307111706736.png)

使用Access Token 访问保护资源。 返回用户的信息。

![image.png](https://assets.happtim.com/image/n3dc/202307111706391.png)
