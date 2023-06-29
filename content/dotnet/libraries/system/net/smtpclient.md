+++
date = "2023-06-28"
title = "smtpclient 使用ssl协议发送邮件失败"

categories = ['dotnet']
+++

在开发中有用到使用STMP协议发送邮件的需求，邮件服务器必须使用开启ssl协议。

```csharp
string smtpServer = "gz-smtp.qcloudmail.com";  
int smtpPort = 465;  
  
string fromAddress = "no-reply@mail.deng-shen.com";  
string toAddress = "526821398@qq.com";  
string subject = "Hello, World!";  
string body = "This is the body of the email.";  
  
string username = "no-reply@mail.deng-shen.com";  
string password = "password";  
  
MailMessage mailMessage = new MailMessage(fromAddress, toAddress)  
{  
    Subject = subject,  
    Body = body,  
};  
mailMessage.Headers.Add("Content-Type","text/html; charset=UTF-8");  
  
SmtpClient smtpClient = new SmtpClient(smtpServer, smtpPort)  
{  
    UseDefaultCredentials  = false,  
    Credentials = new NetworkCredential(username, password),  
    EnableSsl = true,  
}  
  
try{  
    smtpClient.Send(mailMessage);    Console.WriteLine("Email sent successfully!");  
}  
catch (Exception ex)  
{    Console.WriteLine("Failed to send email: " + ex.Message);  
}
```

但是按照配置好地址发送邮件时，总是抛出异常。**"Unable to read data from the transport connection: The connection was closed."**  调用堆栈如下：

```

at System.Net.Mail.SmtpReplyReaderFactory.ProcessRead(Byte[] buffer, Int32 offset, Int32 read, Boolean readLine)  
at System.Net.Mail.SmtpReplyReaderFactory.ReadLines(SmtpReplyReader caller, Boolean oneLine)  
at System.Net.Mail.SmtpReplyReaderFactory.ReadLine(SmtpReplyReader caller)  
at System.Net.Mail.SmtpConnection.GetConnection(String host, Int32 port)  
at System.Net.Mail.SmtpTransport.GetConnection(String host, Int32 port)  
at System.Net.Mail.SmtpClient.GetConnection()  
at System.Net.Mail.SmtpClient.Send(MailMessage message)
```

查看了下调用堆栈的源码：
大致意思先建立tcp链接，如何设置enableSsl 在发送命令开启加密协议，这与一些现代的邮件协议不支持。

```csharp
...
InitializeConnection(host, port);  
_responseReader = new SmtpReplyReaderFactory(_networkStream);  
LineInfo lineInfo = _responseReader.GetNextReplyReader().ReadLine();  
SmtpStatusCode statusCode = lineInfo.StatusCode;  
if (statusCode != SmtpStatusCode.ServiceReady)  
{  
	throw new SmtpException(lineInfo.StatusCode, lineInfo.Line, serverResponse: true);  
}  
...
if (_enableSsl)  
{  
	if (!_serverSupportsStartTls && !(_networkStream is TlsStream))  
	{  
		throw new SmtpException(SR.MailServerDoesNotSupportStartTls);  
	}  
	StartTlsCommand.Send(this);  
	TlsStream tlsStream = new TlsStream(_networkStream, _tcpClient.Client, host, _clientCertificates);  
	tlsStream.AuthenticateAsClient();  
	_networkStream = tlsStream;  
	_responseReader = new SmtpReplyReaderFactory(_networkStream);  
	_extensions = EHelloCommand.Send(this, _client._clientDomain);  
	ParseExtensions(_extensions);  
}
...
```

查看抓包日志，也没有使用加密的协议。

![image.png](https://assets.happtim.com/image/n3dc/202306281538336.png)
查看官方文档，提出了一些使用建议。

>我们不建议您在新的开发中使用SmtpClient类，因为SmtpClient不支持许多现代协议。建议使用MailKit或其他库。

使用MailKit 库之后，抓包结果显示使用tsl协议了
![image.png](https://assets.happtim.com/image/n3dc/202306281555211.png)
