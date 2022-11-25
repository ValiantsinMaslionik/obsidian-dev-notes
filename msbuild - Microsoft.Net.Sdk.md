#msbuild/netsdk

---

[MSBuild reference for .NET SDK projects](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/msbuild-props)

[[#PropertyGroup]]

## Packaging

[Add additional files in nuget package](zDOC_msbuild-additional-files-in-nuget-package.mhtml)
[Shipping a cross-platform MSBuild task in a NuGet package](zDOC_msbuild-Shipping-crossplatform-task-nuget.mhtml)

## Properties

Name|Description|Link
--|--|--
CopyLocalLockFileAssemblies|If you set this property to `true`, any NuGet package dependencies are copied to the output directory.|[Learn.MS](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/msbuild-props#copylocallockfileassemblies)
IncludeBuildOutput|Determines if the `lib` folder should be added to the package|
IsCrossTargetingBuild|Returns `true` if the current build is a multi targeting build|
IsInnerBuild|Returns `true` if the current build stage is 'internal' during a multi targeting build|
UsingMicrosoftNETSdk|Returns `true` if current project is SDK-style project|[StackOverflow](https://stackoverflow.com/questions/52400116/msbuild-condition-for-new-sdk-style-vs-old-project-format)
