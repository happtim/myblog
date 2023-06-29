+++
date = '2023-06-17'
title = 'identity 介绍'

tags = ['aspnetcore','identity']
categories = ['dotnet']
+++

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

## ASP.NET Core Identity 架构

ASP.NET Core Identity由两个主要类别的类组成。它们是管理者（**Managers**）和存储 （**Stores**）。

### Managers

**Managers** 管理与身份相关的数据，如创建用户、添加角色等。它包括`UserManager`、`RoleManager`、`SignInManager`等诸如此类的类。

#### User Manager


**UserManager**是一个具体的类，用于管理用户。该类可以创建、更新和删除用户。它具有通过用户ID、用户名和电子邮件查找用户的方法。UserManager还提供了添加声明、删除声明、添加和删除角色等功能。它还可以生成密码哈希值，验证用户等。

#### SignIn Manager

**SignInManager**负责用户在应用程序中的登录和退出功能。它包含像`SignInAsync`、`SignOutAsync`等方法。在登录过程中，它会根据用户数据创建一个新的`ClaimsPrincipal`。然后将HttpContext.User属性设置为新的`ClaimsPrincipal`。接着，它会对Principal进行序列化、加密，并将它作为一个cookie保存起来。

Identity Api 使用Cookie Authentication管理认证，服务端在响应中将cookie发送到浏览器。浏览器在每个请求中将其返回给服务器。

### Stores

存储将用户、角色等持久化到数据源。

ASP.NET Core Identity API使用Entity Framework Core将用户信息存储在SQL Server数据库中。但你可以更改它以使用不同的数据库或ORM。

身份系统将存储和管理器解耦。因此，我们可以轻松地更改数据库提供程序，而不会中断整个应用程序。

下图显示了您的Web应用程序如何与管理器和存储交互。

