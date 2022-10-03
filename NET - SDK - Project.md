#NET/SDK

---

## Adding project reference

```xml
<ItemGroup>
	<ProjectReference Include="..\path\name.csproj" />
</ItemGroup>
```

## Enabling a specific language version compiler

> To get all supported lang versions: _csc -langversion:?_

```xml
<LangVersion>7.3</LangVersion>
```

LangVersion|Description
--|--
7, 7.1, 8,...|Entering a specific version number will use that compiler if it has been installed
latestmajor|Uses the highest major number
latest|Uses the highest major and highest minor number
preview|Uses the highest available preview version

## Enabling nulable and non-nullable reference types

```xml
<PropertyGroup>
	<Nullable>enable</Nullable>
</PropertyGroup>
```

```cs
#nullable disable
...
#nullable enable

```

