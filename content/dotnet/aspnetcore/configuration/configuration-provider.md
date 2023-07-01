+++
Date = "2023-07-01"
Title = "ASP.NET Core中的配置"

tags = ['aspnetcore','configuration']
+++

## Wait is Configuration

配置是应用程序特定的参数或初始化设置。这些设置与代码分开存储在一个外部文件中。这使得开发人员和管理员能够对应用程序的运行方式进行控制和灵活性。

例如，连接到数据库所需的连接字符串存储在一个配置文件中。通过修改连接字符串，您可以更改数据库名称、位置等，而无需修改代码。

在ASP.NET Core中，应用程序配置是通过一个或多个配置提供程序来完成的。配置提供程序使用各种配置源从键值对中读取配置数据：

- Settings files, such as `appsettings.json`
- Environment variables
- Azure Key Vault
- Azure App Configuration
- Command-line arguments
- Custom providers, installed or created
- Directory files
- In-memory .NET objects

## Application configuration providers

以下代码按照它们被添加的顺序，显示在配置提供程序：

```csharp
var builder = WebApplication.CreateBuilder();  
  
var app = builder.Build();

var scopeFactory = app.Services.GetService<IServiceScopeFactory>();

using( var scope = scopeFactory.CreateScope() )
{
	var config  = scope.ServiceProvider.GetService<IConfiguration>();

	var configRoot = (IConfigurationRoot) config;
	
	foreach (var provider in configRoot.Providers.ToList())
	{
		provider.Dump(provider.GetType().Name);
	}
}
```

IConfiguration 和  IConfigurationRoot 有什么不同？

`IConfigurationRoot`是`IConfiguration`的实现，并提供了更多的功能。通常情况下，你可以使用`IConfiguration`来读取配置值，但在某些情况下，你可能需要访问更高级的功能，如获取Providers或实时更新配置值，这时可以使用`IConfigurationRoot`。

## WebApplication Default Configuration

上述代码中我们什么也做，就可以获取`IConfiguration`,其实在`WebApplication`创建时就有默认的配置。

```csharp
builder.ConfigureHostConfiguration(delegate(IConfigurationBuilder config)
	{
		config.AddEnvironmentVariables("DOTNET_");
		if (args != null && args.Length > 0)
		{
			config.AddCommandLine(args);
		}
	});
	builder.ConfigureAppConfiguration(delegate(HostBuilderContext hostingContext, IConfigurationBuilder config)
	{
		IHostEnvironment hostingEnvironment = hostingContext.HostingEnvironment;
		bool reloadConfigOnChangeValue = GetReloadConfigOnChangeValue(hostingContext);
		config.AddJsonFile("appsettings.json", optional: true, reloadConfigOnChangeValue).AddJsonFile("appsettings." + hostingEnvironment.EnvironmentName + ".json", optional: true, reloadConfigOnChangeValue);
		if (hostingEnvironment.IsDevelopment())
		{
			string applicationName = hostingEnvironment.ApplicationName;
			if (applicationName != null && applicationName.Length > 0)
			{
				Assembly assembly = Assembly.Load(new AssemblyName(hostingEnvironment.ApplicationName));
				if ((object)assembly != null)
				{
					config.AddUserSecrets(assembly, optional: true, reloadConfigOnChangeValue);
				}
			}
		}
		config.AddEnvironmentVariables();
		if (args != null && args.Length > 0)
		{
			config.AddCommandLine(args);
		}
	})
```

初始化的WebApplicationBuilder（构建器）按照优先级从高到低为应用程序提供默认配置。

1. 使用命令行配置提供程序的命令行参数。
2. 使用非前缀环境变量配置提供程序。
3. 在应用程序在`Development`环境中运行时使用用户机密。
4. 使用JSON配置提供程序的appsettings.{Environment}.json。例如，appsettings.Production.json和appsettings.Development.json。
5. 使用JSON配置提供程序的appsettings.json。
6. 主机配置。

其实添加的顺序是相反的。 后面添加读取的时候会覆盖前面的相同配置。

## Config Custom Configuration

内置的默认配置对大多数应用程序来说已经足够。我们还可以轻松地扩展它来满足我们的需求。

```csharp 
var builder = WebApplication.CreateBuilder();

builder.Configuration.AddInMemoryCollection(
	new[] {
		new KeyValuePair<string, string>("Jwt:Secret", "F-JaNdRfUserjd89#5*6Xn2r5usErw8x/A?D(G+KbPeShV"),
		new KeyValuePair<string, string>("Jwt:Issuer", "http://localhost:5000/"),
		new KeyValuePair<string, string>("Jwt:Audience", "http://localhost:5000/"),
		new KeyValuePair<string, string>("Jwt:AccessTokenExpiration", "5"),
		new KeyValuePair<string, string>("Jwt:RefreshTokenExpiration", "10"),
	});
	
builder.Configuration.AddJsonFile("custom.json");

var app = builder.Build();
```