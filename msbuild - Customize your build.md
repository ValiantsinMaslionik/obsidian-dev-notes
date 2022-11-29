#msbuild 

---

[Learn.MS](https://learn.microsoft.com/en-us/visualstudio/msbuild/customize-your-build?view=vs-2022)

Directory.Build.props 
Directory.Build.targets

## How to import parent Directory.Build.props

```xml
<Import Project="$([MSBuild]::GetPathOfFileAbove('Directory.Build.props', '$(MSBuildThisFileDirectory)../'))" />
```