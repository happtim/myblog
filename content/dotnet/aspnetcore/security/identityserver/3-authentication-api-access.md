+++
Date = "2023-07-13"
Title = "IdentityServer同时实现认证和访问API"

tags = ['OIDC','identityserver']
+++

OpenID Connect和OAuth的结合非常简洁；您可以通过令牌服务在一次交互中实现用户认证和API访问。

在上一节中，登录过程中的令牌请求仅要求身份资源，即仅要求范围如个人资料和开放ID。在此快速入门中，您将在该请求中添加用于API资源的范围。IdentityServer将回复两个令牌：
- 身份令牌，包含了关于认证过程和会话的信息，并且
- 访问令牌，允许代表登录用户访问API。

## Modifying the client configuration

身份服务器中的客户端配置需要两个简单的更新。
- 将api1资源添加到允许的范围列表中，以便客户端具有访问权限。
- 通过设置AllowOfflineAccess标志启用对刷新令牌的支持。

### 为什么要设置 AllowOfflineAccess
- offline_access 作用是请求 refresh_token。refresh_token 是一个长期有效的 token,用于当 access_token 过期后,能够通过 refresh_token 来重新获取一个新的 access_token,而无需用户重新登录授权。
- 如果客户端需要能够在用户不在线的情况下,继续访问用户数据,那么就需要请求 offline_access 来获取 refresh_token。

```csharp
new Client
{
    ClientId = "web",
    ClientSecrets = { new Secret("secret".Sha256()) },

    AllowedGrantTypes = GrantTypes.Code,
            
    // where to redirect to after login
    RedirectUris = { "https://localhost:5002/signin-oidc" },

    // where to redirect to after logout
    PostLogoutRedirectUris = { "https://localhost:5002/signout-callback-oidc" },
    
    AllowOfflineAccess = true,

    AllowedScopes = new List<string>
    {
        IdentityServerConstants.StandardScopes.OpenId,
        IdentityServerConstants.StandardScopes.Profile,
        "api1"
    }
}

```


## Modifying the Web client

现在配置客户端请求api1的访问权限和离线访问令牌，方法是请求api1和offline_access范围。这在src/WebClient/Program.cs中的OpenID Connect处理程序配置中完成。

```csharp
builder.Services.AddAuthentication(options =>
{
    options.DefaultScheme = "Cookies";
    options.DefaultChallengeScheme = "oidc";
})
    .AddCookie("Cookies")
    .AddOpenIdConnect("oidc", options =>
    {
        options.Authority = "https://localhost:5001";

        options.ClientId = "web";
        options.ClientSecret = "secret";
        options.ResponseType = "code";

        options.SaveTokens = true;

        options.Scope.Clear();
        options.Scope.Add("openid");
        options.Scope.Add("profile");
        options.Scope.Add("api1");
        options.Scope.Add("offline_access");
        options.GetClaimsFromUserInfoEndpoint = true;
    });

```


## Using the access token

现在你将使用访问令牌来授权WebClient向Api发送请求。

将src/WebClient/Pages/CallApi.cshtml.cs的内容按照以下方式更新：

```csharp
public class CallApiModel : PageModel
{
    public string Json = string.Empty;

    public async Task OnGet()
    {
        var accessToken = await HttpContext.GetTokenAsync("access_token");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        var content = await client.GetStringAsync("https://localhost:6001/identity");

        var parsed = JsonDocument.Parse(content);
        var formatted = JsonSerializer.Serialize(parsed, new JsonSerializerOptions { WriteIndented = true });

        Json = formatted;
    }
}

```

确保 IdentityServer 和 Api 项目正在运行，启动 WebClient 并在身份验证后请求 /CallApi。

![image.png](https://assets.happtim.com/image/n3dc/202307132328134.png)

## 保护API几种方式

### 通过JWT保护

在上述案例中，我们只关心确保访问令牌来自您信任的IdentityServer。JWT的配置如下。

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                // base-address of your identityserver
                options.Authority = "https://demo.duendesoftware.com";

                // audience is optional, make sure you read the following paragraphs
                // to understand your options
                options.TokenValidationParameters.ValidateAudience = false;

                // it's recommended to check the type header to avoid "JWT confusion" attacks
                options.TokenValidationParameters.ValidTypes = new[] { "at+jwt" };
            });
    }
}

```

仅仅确保令牌来自受信任的发行者对于大多数情况来说并不足够。在更复杂的系统中，您将拥有多个资源和多个客户端。并非每个客户端都有权限访问每个资源。

在OAuth中，有两种互补的机制可以嵌入有关令牌的“功能性”更多信息 - audience和scope。



