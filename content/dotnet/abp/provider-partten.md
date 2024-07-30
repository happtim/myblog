
## Provider设计模式

"provider" 通常用于描述一种设计模式，即 "Provider 模式"。Provider 模式是一种用于将具体实现与客户端分离的设计模式。它通常通过接口或抽象类定义服务或资源的行为，然后由具体的提供者类（Provider Class）实现这些接口或抽象类。这样可以方便地替换或扩展服务或资源的实现，而无需修改客户端代码。

考虑一个简化的日志系统的例子：

1. 定义一个抽象的日志接口：

``` c#
public interface Logger {  
    void log(String message);  
}
```

2. 实现具体的日志提供者类：

``` c#
public class ConsoleLogger implements Logger {  
    public void log(String message) {  
        System.out.println("ConsoleLogger: " + message);  
    }  
}  

public class FileLogger implements Logger {  
    public void log(String message) {  
        // 写入文件逻辑  
        System.out.println("FileLogger: " + message);  
    }  
}
```

3. LoggerProvider 类来获取 Logger 实例：

``` c#
public class LoggerProvider {  
    private static Logger logger = new ConsoleLogger(); // 默认的日志实现  

    public static Logger getLogger() {  
        return logger;  
    }  

    public static void setLogger(Logger newLogger) {  
        logger = newLogger;  
    }  
}
```

4. 客户端代码：

``` c#
public class Application {  
    public static void main(String[] args) {  
        Logger logger = LoggerProvider.getLogger();  
        logger.log("This is a log message");  

        LoggerProvider.setLogger(new FileLogger());  
        logger = LoggerProvider.getLogger();  
        logger.log("This is another log message");  
    }  
}
```

## Abp Provider

abp中有两类的Provider，分别为：
1. options的provider
2. definition provider 

definition provider 和contributor功能类似，只是definition的provider是针对于定义的
好的。如：`PermissionDefinition`
## Abp中的Options Provider

该Provider一般为相应的Options提供参数。
该参数一般为Dictionary，key为string，值为具体配置参数，可以任意数据类型。
在依赖的模块中，为Dictionary增加新的配置key-value。
Prover提供获取指定key的方法，如果指定key不存在则抛出异常。

### Abp.RemoteServices

options 定义

```c#
public class AbpRemoteServiceOptions
{
    public RemoteServiceConfigurationDictionary RemoteServices { get; set; }

    public AbpRemoteServiceOptions()
    {
        RemoteServices = new RemoteServiceConfigurationDictionary();
    }
}

public class RemoteServiceConfigurationDictionary : Dictionary<string, RemoteServiceConfiguration>
{
    public const string DefaultName = "Default";

    public RemoteServiceConfiguration Default {
        get => this.GetOrDefault(DefaultName);
        set => this[DefaultName] = value;
    }

    [NotNull]
    public RemoteServiceConfiguration GetConfigurationOrDefault(string name)
    {
        return this.GetOrDefault(name)
               ?? Default
               ?? throw new AbpException($"Remote service '{name}' was not found and there is no default configuration.");
    }

    [CanBeNull]
    public RemoteServiceConfiguration GetConfigurationOrDefaultOrNull(string name)
    {
        return this.GetOrDefault(name)
               ?? Default;
    }
}
```

在Module中添加值

```c#
Configure<AbpRemoteServiceOptions>(options =>
{
	options.RemoteServices.Default = new RemoteServiceConfiguration("/");
});
```


### Abp.Localization

options 定义

```c#
public class AbpLocalizationOptions
{
    public LocalizationResourceDictionary Resources { get; }

    /// <summary>
    /// Used as the default resource when resource was not specified on a localization operation.
    /// </summary>
    public Type DefaultResourceType { get; set; }

    public ITypeList<ILocalizationResourceContributor> GlobalContributors { get; }

    public List<LanguageInfo> Languages { get; }

    public Dictionary<string, List<NameValue>> LanguagesMap { get; }

    public Dictionary<string, List<NameValue>> LanguageFilesMap { get; }

    public bool TryToGetFromBaseCulture { get; set; }

    public bool TryToGetFromDefaultCulture { get; set; }

    public AbpLocalizationOptions()
    {
        Resources = new LocalizationResourceDictionary();
        GlobalContributors = new TypeList<ILocalizationResourceContributor>();
        Languages = new List<LanguageInfo>();
        LanguagesMap = new Dictionary<string, List<NameValue>>();
        LanguageFilesMap = new Dictionary<string, List<NameValue>>();
        TryToGetFromBaseCulture = true;
        TryToGetFromDefaultCulture = true;
    }
}
```

provider定义

``` c#
public class DefaultLanguageProvider : ILanguageProvider, ITransientDependency
{
    protected AbpLocalizationOptions Options { get; }

    public DefaultLanguageProvider(IOptions<AbpLocalizationOptions> options)
    {
        Options = options.Value;
    }

    public Task<IReadOnlyList<LanguageInfo>> GetLanguagesAsync()
    {
        return Task.FromResult((IReadOnlyList<LanguageInfo>)Options.Languages);
    }
}
```

在其他模块中的使用

```c#
Configure<AbpLocalizationOptions>(options =>
{
	options.Resources
		.Add<MvcTestResource>("en")
		.AddBaseTypes(
			typeof(AbpUiResource),
			typeof(AbpValidationResource)
		).AddVirtualJson("/Volo/Abp/AspNetCore/Mvc/Localization/Resource");

	options.Languages.Add(new LanguageInfo("en", "en", "English"));
	options.Languages.Add(new LanguageInfo("hu", "hu", "Magyar"));
	options.Languages.Add(new LanguageInfo("ro-RO", "ro-RO", "Română"));
	options.Languages.Add(new LanguageInfo("sk", "sk", "Slovak"));
	options.Languages.Add(new LanguageInfo("tr", "tr", "Türkçe"));
	options.Languages.Add(new LanguageInfo("el", "el", "Ελληνικά"));
});
```

## Abp中的definition-provider示例

在Abp中，一些模块提供出扩展的能力，需要依赖与该模块上进行扩展。

通常在Options中定义Providers的列表，在Provider中实现扩展模块方法。 一般Provider中的方法参数中中有一个Context，用于传递参数，或者实现依赖注入。

Options的Provider的列表的添加，一般使用AutoFact的注册事件来添加。所以Provider都要依赖注入方法。

在创建初始化定义的Provider的方法时时候，一般准备好Context中的参数，和依赖注入的socpe，循环options的privider，调用方法获取提供的值。

Provider中获取的值，一般存在对应的Manager中，可以有获取方法，来获取provider中值。


### Dignite.Notification 模块说明

Provider定义

``` c#
public interface INotificationDefinitionProvider
{
    void Define(INotificationDefinitionContext context);
}
```

Options定义
Options只定义了Provider，并未存储具体Definition，值存储在NotificationDefinitionManager中

``` c#
public class NotificationOptions
{
    public ITypeList<INotificationDefinitionProvider> DefinitionProviders { get; private set; }

    public NotificationOptions()
    {
        DefinitionProviders = new TypeList<INotificationDefinitionProvider>();
    }
}
```

provider方法执行。

``` c#
    protected virtual IDictionary<string, NotificationDefinition> CreateNotificationDefinitions()
    {
        var notifications = new Dictionary<string, NotificationDefinition>();

        using (var scope = ServiceProvider.CreateScope())
        {
            var providers = Options
                .DefinitionProviders
                .Select(p => scope.ServiceProvider.GetRequiredService(p) as INotificationDefinitionProvider)
                .ToList();

            foreach (var provider in providers)
            {
                provider.Define(new NotificationDefinitionContext(notifications));
            }
        }

        return notifications;
    }
```

Provider 在Module中添加

``` c#
public class DigniteAbpNotificationsModule : AbpModule
{
    public override void PreConfigureServices(ServiceConfigurationContext context)
    {
        AutoAddDefinitionProviders(context.Services);
    }

    private static void AutoAddDefinitionProviders(IServiceCollection services)
    {
        var definitionProviders = new List<Type>();

        services.OnRegistered(context =>
        {
            if (typeof(INotificationDefinitionProvider).IsAssignableFrom(context.ImplementationType))
            {
                definitionProviders.Add(context.ImplementationType);
            }
        });

        services.Configure<NotificationOptions>(options =>
        {
            options.DefinitionProviders.AddIfNotContains(definitionProviders);
        });
    }
}
```

### Volo.Abp.Settings

Provider定义

```c#
public interface ISettingDefinitionProvider
{
    void Define(ISettingDefinitionContext context);
}
```

Options 定义

```c#
public class AbpSettingOptions
{
    public ITypeList<ISettingDefinitionProvider> DefinitionProviders { get; }

    public ITypeList<ISettingValueProvider> ValueProviders { get; }

    public AbpSettingOptions()
    {
        DefinitionProviders = new TypeList<ISettingDefinitionProvider>();
        ValueProviders = new TypeList<ISettingValueProvider>();
    }
}
```

Provider中执行方法

```c#
    protected virtual IDictionary<string, SettingDefinition> CreateSettingDefinitions()
    {
        var settings = new Dictionary<string, SettingDefinition>();

        using (var scope = ServiceProvider.CreateScope())
        {
            var providers = Options
                .DefinitionProviders
                .Select(p => scope.ServiceProvider.GetRequiredService(p) as ISettingDefinitionProvider)
                .ToList();

            foreach (var provider in providers)
            {
                provider.Define(new SettingDefinitionContext(settings));
            }
        }

        return settings;
    }
```

在Module中的添加Provider。

``` c#
    private static void AutoAddDefinitionProviders(IServiceCollection services)
    {
        var definitionProviders = new List<Type>();

        services.OnRegistered(context =>
        {
            if (typeof(ISettingDefinitionProvider).IsAssignableFrom(context.ImplementationType))
            {
                definitionProviders.Add(context.ImplementationType);
            }
        });

        services.Configure<AbpSettingOptions>(options =>
        {
            options.DefinitionProviders.AddIfNotContains(definitionProviders);
        });
    }
```


### Abp.Feature

同上

### Abp.Perimission

同上