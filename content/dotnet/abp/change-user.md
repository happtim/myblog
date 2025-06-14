
通过userId切换CurrentUser。

```c#
Guid userId;
using (_currentPrincipalAccessor.Change(new ClaimsPrincipal(new ClaimsIdentity(new Claim[]
{
	new Claim(AbpClaimTypes.UserId,userId.ToString())
})))) 
{
	//Do Something
}
```
