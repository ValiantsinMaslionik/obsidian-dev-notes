#NET/SDK

---

[Learn.MS](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview)

## ItemGroup

Name|Description|Link
--|--|--
LangVersion|Specifies language version for the project|[[#Enabling a specific language version compiler]]
Nullable|Enables or disables non-nullable reference types|[[#Enabling nulable and non-nullable reference types]]
PackageReference Include="PACKAGE_ID" Version="PACKAGE_VERSION"|Adds nuget package reference|[Learn.MS](https://learn.microsoft.com/en-us/dotnet/core/tools/dependencies)
ProjectReference Include="..\\path\\name.csproj"|Adds project reference|

### Enabling a specific language version compiler

> To get all supported lang versions: csc -langversion:?

LangVersion|Description
--|--
7, 7.1, 8,...|Entering a specific version number will use that compiler if it has been installed
latestmajor|Uses the highest major number
latest|Uses the highest major and highest minor number
preview|Uses the highest available preview version

### Enabling nulable and non-nullable reference types

```cs
#nullable disable
...
#nullable enable

```

