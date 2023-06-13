#netcore/sdk

---

# Environment variables 

https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-environment-variables

# global.json

The _global.json_ file allows you to define which .NET SDK version is used when you run .NET CLI commands. Selecting the .NET SDK version is independent from specifying the runtime version a project targets.

```json
{
	"sdk":
	{
		"version": "6.0.300",
		"rollForward": "latestFeature"
	}
}
```

https://learn.microsoft.com/en-us/dotnet/core/tools/global-json

# SDK errors

https://learn.microsoft.com/en-us/dotnet/core/tools/sdk-errors/