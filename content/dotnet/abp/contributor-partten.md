在ABP框架中，Provider和Contributor是两个不同的概念，它们在程序设计中有不同的用途和特点。让我们来详细比较一下：

Provider（提供者）从Options中获取数据。
Contributor（贡献者），从Contributor中获取数据，更加具有扩展性。

和Provider类似，Contributor的配置一般存放到Options中。
### Abp.SettingManagement

定义Contributor

``` c#
public interface ISettingPageContributor
{
    Task ConfigureAsync(SettingComponentCreationContext context);
}

public class SettingComponentCreationContext : IServiceProviderAccessor
{
    public IServiceProvider ServiceProvider { get; }

    public List<SettingComponentGroup> Groups { get; }

    public SettingComponentCreationContext(IServiceProvider serviceProvider)
    {
        ServiceProvider = serviceProvider;

        Groups = new List<SettingComponentGroup>();
    }
}
```

Options中定义Contributor

```c#
public class SettingManagementComponentOptions
{
    public List<ISettingComponentContributor> Contributors { get; }

    public SettingManagementComponentOptions()
    {
        Contributors = new List<ISettingComponentContributor>();
    }
}
```

在 `SettingManagement.razor` 的初始化的时候，进行Contributors。获取包含所有数据的SettingComponentCreationContext。

``` c#
    protected async override Task OnInitializedAsync()
    {
        BreadcrumbItems.Add(new BreadcrumbItem(@L["Settings"]));

        SettingComponentCreationContext = new SettingComponentCreationContext(ServiceProvider);

        foreach (var contributor in Options.Contributors)
        {
            await contributor.ConfigureAsync(SettingComponentCreationContext);
        }

        SettingItemRenders.Clear();

        SelectedGroup = GetNormalizedString(SettingComponentCreationContext.Groups.First().Id);
    }
```

### Abp.UI.Navigation

定义Contributor

```c#
public interface IMenuContributor
{
    Task ConfigureMenuAsync(MenuConfigurationContext context);
}

public class MenuConfigurationContext : IMenuConfigurationContext
{
    public IServiceProvider ServiceProvider { get; }

	public ApplicationMenu Menu { get; }
}
```

Options中定义Contributor

```c#
public class AbpNavigationOptions
{
    [NotNull]
    public List<IMenuContributor> MenuContributors { get; }

    /// <summary>
    /// Includes the <see cref="StandardMenus.Main"/> by default.
    /// </summary>
    public List<string> MainMenuNames { get; }

    public AbpNavigationOptions()
    {
        MenuContributors = new List<IMenuContributor>();

        MainMenuNames = new List<string>
            {
                StandardMenus.Main
            };
    }
}
```

当获取菜单时的初始化

```c#
protected virtual async Task<ApplicationMenu> GetInternalAsync(string name)
{
	var menu = new ApplicationMenu(name);

	using (var scope = ServiceScopeFactory.CreateScope())
	{
		using (RequirePermissionsSimpleBatchStateChecker<ApplicationMenuItem>.Use(new RequirePermissionsSimpleBatchStateChecker<ApplicationMenuItem>()))
		{
			var context = new MenuConfigurationContext(menu, scope.ServiceProvider);

			foreach (var contributor in Options.MenuContributors)
			{
				await contributor.ConfigureMenuAsync(context);
			}

			await CheckPermissionsAsync(scope.ServiceProvider, menu);
		}
	}

	NormalizeMenu(menu);
	NormalizeMenuGroup(menu);

	return menu;
}
```