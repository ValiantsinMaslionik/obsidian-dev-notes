#netcore/practicies

---

## Hadling platforms that do not support an API

```cs
try
{
	...
}
catch (PlatformNotSupportedException)
{

}
```

## Checking OS name at runtime

[OperationSystem](https://learn.microsoft.com/en-us/dotnet/api/system.operatingsystem?view=net-6.0)

```cs
if(OperationSystem.IsWindows())
{
...
}
```