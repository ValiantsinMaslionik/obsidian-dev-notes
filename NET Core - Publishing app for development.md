#netcore

---

There three ways to publish and deploy a .NET application.

1) Framework-dependent deployment (FDD)
2) Framework-dependent executables (FDEs)
3) Self-contained

Choose **1-2** to deploy an application and its package dependencies, but not .NET itself, then you relay on .NET already being on the target computer. This works well for web applications deployed to a server because .NET and lots of other web aopplications are likely already on the server.

Choose **3** to perform a self-contained deployment. This includes both application itself and .NET runtime.

## App to publish

Project file

```xml
<Project Sdk="Microsoft.NET.SDK">
	<PropertyGroup>
		<OutputType>Exe</OutputType>
		<TargetFramework>net6.0</TargetFramework>
		<!-- Target runtime identifiers -->
		<RuntimeIdentifiers>win10-x64;osx-x64;rhel.7.4-x64</RuntimeIdentifiers>
	</PropertyGroup>
</Project>
```

