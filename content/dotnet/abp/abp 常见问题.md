## UOW

`IDeviceAppService` 会依赖于 `IDeviceRepository` 如果在一个Worker中不加UOW的话，

第一行执行完之后 就会将deviceAppService内的Repository释放掉，第二次再去Resolve的时候 `IDeviceRepository` 就是一个释放的状态。 需要在Worker上 添加UOW标志。

``` csharp
var deviceAppService = workerContext.ServiceProvider.GetRequiredService<IDeviceAppService>();
                    var deviceRepository = workerContext.ServiceProvider.GetRequiredService<IDeviceRepository>();
                    var elevator = await deviceRepository.FirstAsync();
```

## EntityUpdatedEventData

在更新DeviceProperty 的时候，并没有更新Device 也会造成事件的调用。 需要将获取实体移除。

``` csharp
public async Task SetPropertyValueAsync(Guid id, SetPropertyValueDto valueDto)
    {
        var device = await Repository.GetAsync(id);

        var driver = _thingInstanceManager.GetDriver(id);

        await driver.SetPropertyValueAsync(valueDto.PropertyIdentifier, valueDto.Value);
    }
```

## Clock

对于筛选时时间不对，需要在ApplicationModule中添加。

``` csharp
	Configure<AbpClockOptions>(x => x.Kind = DateTimeKind.Local);
```