## Module支持可扩展


1. Domain.Shared 应该有 ObjectExtending 的扩展方法。来扩展该模块以及该模块的哪些实体具备扩展的能力。
2. Domain 应该在PostConfigureServices 方法配置 ApplyEntityConfigurationToEntity 方法。
3. Application.Contracts 中 PostConfigureServices 方法配置 ApplyEntityConfigurationToApi。
4. EntityFrameworkCore 中 DbContextModelCreatingExtensions 方法中 为每个实体增加  ApplyObjectExtensionMappings 方法，并在最后的增加对Contextext扩展。

```csharp
builder.Entity<QA_Question>(b =>
{
	b.ToTable(options.TablePrefix + "Questions", options.Schema);
	b.ConfigureByConvention();
	//...

	//Call this in the end of buildAction.
	b.ApplyObjectExtensionMappings();
});

//...

//Call this in the end of ConfigureQA.
builder.TryConfigureObjectExtensions<QADbContext>();
```


## 如何扩展Module

1. Domain.Shared 项目中 xxxModuleExtensionConfigurator 方法中，对需要扩展模块实体进行扩展。
2. Domain.Shared 项目中 ObjectExtendings 目录中，创建 如: UserConsts 方法进行字段的扩展。
3. 如果需要单独数据库字段。 需要在EntityFrameworkCore 项目中， xxxEfCoreEntityExtensionMappings 方法中配置属性信息，并数据迁移。
4. 对Entity实体进行属性扩展。方便程序设置，获取值。