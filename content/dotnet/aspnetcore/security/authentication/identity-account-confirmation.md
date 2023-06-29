+++
Date = "2023-06-28"
Title = "ASP.NET Core identity 账户确认和密码找回"

tags = ['aspnetcore','identity']
categories = ['dotnet']
+++

## 项目示例

该示例代码使用的MVC方式提供界面。

| 地址 | .Net版本 |
| -------- | -------- |
|   [代码地址](https://github.com/happtim/mylinqpad/tree/main/AspNetCore/Security/Authentication/IdentitySample.Mvc)      | .Net7    | 



### Configure an email provider

本例子使用腾讯云提供邮件服务，他使用SMTP服务发送邮件。

创建一个类来获取安全电子邮件密钥。对于此示例，创建`Services/AuthMessageSenderOptions.cs`文件。

```csharp
public class AuthMessageSenderOptions
{
	public string? SMTPPassword{ get; set; }

	public string? SMTPUsername { get; set; }
}
```

#### Configure SMTP user secrets

设置`SMTPPassword`和`SMTPUsername`通过[secret-manager tool](https://learn.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-7.0).

```powershell
PM> dotnet user-secrets set SMTPUsername <key>
Successfully saved SMTPUsername = <key> to the secret store.
PM> dotnet user-secrets set SMTPPassword <key>
Successfully saved SMTPPassword = <key> to the secret store.
```

注意如果有特殊字符如`$`会截断后面的，设置好secrets.json 再去配置文件中检查一遍。

在Windows上，Secret Manager将键/值对存储在`%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`目录下的`secrets.json`文件中。

![image.png](https://assets.happtim.com/image/n3dc/202306281841780.png)


在Linux上，Secret Manager将键/值对存储在`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

### Implement IEmailSender

IEmailSender 给编写实现类。这里没有使用系统StmpClient [[smtpclient]]，因为不支持ssl。

```csharp
public Task SendEmailAsync(string email, string subject, string body)
{
	if (string.IsNullOrEmpty(Options.SMTPPassword) || string.IsNullOrEmpty(Options.SMTPUsername) )
	{
		throw new Exception("Null SMTP Password Or Username");
	}

	string smtpServer = "gz-smtp.qcloudmail.com";
	int smtpPort = 465;

	string fromAddress = "no-reply@mail.deng-shen.com";

	// 创建邮件对象
	var message = new MimeMessage();
	message.From.Add(new MailboxAddress("deng-shen", fromAddress));
	message.To.Add(new MailboxAddress("收件人", email));
	message.Subject = subject;

	// 创建邮件正文
	var bodyBuilder = new BodyBuilder();
	bodyBuilder.TextBody = body;

	// 将正文添加到邮件中
	message.Body = bodyBuilder.ToMessageBody();

	try
	{
		// 创建 SmtpClient 对象
		using (var client = new SmtpClient())
		{
			// 连接到 SMTP 服务器
			client.Connect(smtpServer, smtpPort, true);

			// 使用 SMTP 服务器的身份验证凭据进行身份验证
			client.Authenticate(Options.SMTPUsername, Options.SMTPPassword);

			// 发送邮件
			client.Send(message);

			// 断开连接
			client.Disconnect(true);
		}
	}
	catch (Exception ex)
	{
		_logger.LogError("Failed to send email: " + ex.Message);
	}

	// Plug in your email service here to send an email.
	return Task.FromResult(0);
}
```

### Configure app to support email

将以下代码添加到Program.cs文件中：

- 将EmailSender添加为一个 transient scope。
- 注册AuthMessageSenderOptions配置实例。

```csharp
// Add application services.
builder.Services.AddTransient<IEmailSender, AuthMessageSender>();
builder.Services.AddTransient<ISmsSender, AuthMessageSender>();
builder.Services.Configure<AuthMessageSenderOptions>(builder.Configuration);
```

并且配置`AddIdentityCore`参数`o.SignIn.RequireConfirmedAccount`为true

### 修改注册代码发送邮件

sampleMVC 这个例子中注册发送邮件的代码是注释掉的。当我们写好了`IEmailSender`可以解除注释它了。

```csharp
//
// POST: /Account/Register
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Register(RegisterViewModel model, string returnUrl = null)
{
	ViewData["ReturnUrl"] = returnUrl;
	if (ModelState.IsValid)
	{
		var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
		var result = await _userManager.CreateAsync(user, model.Password);
		if (result.Succeeded)
		{
			// For more information on how to enable account confirmation and password reset please visit http://go.microsoft.com/fwlink/?LinkID=532713
			// Send an email with this link
			var code = await _userManager.GenerateEmailConfirmationTokenAsync(user);
			var callbackUrl = Url.Action("ConfirmEmail", "Account", new { userId = user.Id, code = code }, protocol: HttpContext.Request.Scheme);
			await _emailSender.SendEmailAsync(model.Email, "Confirm your account",
				"Please confirm your account by clicking this link: <a href=\"" + callbackUrl + "\">link</a>");
			await _signInManager.SignInAsync(user, isPersistent: false);
			_logger.LogInformation(3, "User created a new account with password.");
			return RedirectToLocal(returnUrl);
		}
		AddErrors(result);
	}

	// If we got this far, something failed, redisplay form
	return View(model);
}
```

## Test Register, confirm email, and reset password

