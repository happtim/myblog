+++
date = '2023-06-17'
title = 'identity'

tags = ['aspnetcore','identity']
categories = ['dotnet']
+++

| 例子地址 | .Net版本 |
| -------- | -------- |
|   IdentitySample.DefaultUI       | .Net7    | 

ASP.NET Core Identity是我们提供的自包含、开箱即用的解决方案。它包括以下内容：

1. Identity Manager：提供用于处理用户（包括声明和登录）和角色的API。
2. Identity Store接口：用于持久化身份信息（用户、声明、登录提供程序和角色）。
3. 针对关系型数据库的默认身份存储实现。您可以选择创建自己的自定义身份存储实现。
4. 身份验证系统（SignInManager）。
5. 用户管理界面（Identity UI）。

ASP.NET Core Identity提供了一套完整的解决方案，方便您管理用户认证、角色和身份信息。

![image.png](http://assets.happtim.com/image/n3dc/202306171548366.png)


>该描述引用 .Net 微软博客。
>[Improvements to auth and identity in ASP.NET Core 8 - .NET Blog (microsoft.com)](https://devblogs.microsoft.com/dotnet/improvements-auth-identity-aspnetcore-8/)

