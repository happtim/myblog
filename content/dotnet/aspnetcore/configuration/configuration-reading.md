+++
Date = "2023-07-01"
Title = "ASP.NET Core中的配置"

tags = ['aspnetcore','configuration']
+++

## Reading the Configuration

在多级分层配置的情况下，我们可以使用 : 来分隔每个级别。
key对大小写不敏感，如果指定key值没有返回null。

### 使用索引器

```csharp
config["Jwt:Issuer"].Dump("Jwt:Issuer");  
config["JWT:Issuer"].Dump("JWT:Issuer");  
config["JWT:Issuerr"].Dump("JWT:Issuerr");
```

### 使用范型

通过`IConfiguration`接口提供的泛型方法，可以直接将配置值转换为指定类型。

```csharp
config.GetValue<int>("Jwt:AccessTokenExpiration").Dump("Jwt:AccessTokenExpiration");
```

### GetSection

`IConfiguration`接口的`GetSection`方法可用于访问配置文件中的特定节。然后可以使用索引器或其他方法来从节中获取值。

```csharp
var jwtConfig =  config.GetSection("Jwt");  
jwtConfig["Issuer"].Dump("Issuer");
```

### GetChildren

`IConfiguration`接口的`GetChildren`方法可用于获取当前配置节点下的所有子节点。可以使用`foreach`循环遍历子节点并获取值。

```csharp
var jwtConfig =  config.GetSection("Jwt");

var children = jwtConfig.GetChildren();
foreach (var child in children)
{
	child.Value.Dump(child.Path);
}
```


### Bind hierarchical configuration data using the options pattern

首选读取相关配置值的方法是使用选项模式。

```csharp
class JwtOptions 
{
	public const string Jwt = "Jwt";
	public string Secret {get;set;} = string.Empty
	public string Issuer {get;set;} = string.Empty
}
```

创建一个上述类的实例。然后使用Bind方法，将配置中的值填充到该类中。

```csharp
var jwtOptions = new JwtOptions();
jwtConfig.Bind(jwtOptions);

jwtOptions.Dump(nameof(jwtOptions));
```

### using then options pattern with DI

在以下代码中，PositionOptions使用Configure方法添加到服务容器，并与配置进行绑定：

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
	
builder.Services.Configure<JwtOptions>(builder.Configuration.GetSection(JwtOptions.Jwt));
	
var app = builder.Build();
```

使用IOptions\<T>注入。其中T是我们配置类。

```csharp
//Options    
var options =  scope.ServiceProvider.GetService<IOptions<JwtOptions>>();  
options.Value.Dump("jwtOptions");
```