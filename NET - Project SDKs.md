#msbuild/sdk

---

[Learn.MS](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview)
[MSBuild reference for .NET SDK projects](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/msbuild-props)

Name|Description|Link
--|--|--
CopyLocalLockFileAssemblies|If you set this property to `true`, any NuGet package dependencies are copied to the output directory.|[Learn.MS](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/msbuild-props#copylocallockfileassemblies)
LangVersion|Specifies language version for the project|[[#Enabling a specific language version compiler]]
IncludeBuildOutput|Determines if the `lib` folder should be added to the package|
IsCrossTargetingBuild|Returns `true` if the current build is a multi targeting build|
IsInnerBuild|Returns `true` if the current build stage is 'internal' during a multi targeting build|
Nullable|Enables or disables non-nullable reference types|[[#Enabling nulable and non-nullable reference types]]
PackageReference Include="PACKAGE_ID" Version="PACKAGE_VERSION"|Adds nuget package reference|[Learn.MS](https://learn.microsoft.com/en-us/dotnet/core/tools/dependencies)
ProjectReference Include="..\\path\\name.csproj"|Adds project reference|
UsingMicrosoftNETSdk|Returns `true` if current project is SDK-style project|[StackOverflow](https://stackoverflow.com/questions/52400116/msbuild-condition-for-new-sdk-style-vs-old-project-format)

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

