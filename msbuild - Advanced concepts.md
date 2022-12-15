#msbuild 

---

[Customize your build](https://learn.microsoft.com/en-us/visualstudio/msbuild/customize-your-build?view=vs-2022)

How to import parent Directory.Build.props:

```xml
<Import Project="$([MSBuild]::GetPathOfFileAbove('Directory.Build.props', '$(MSBuildThisFileDirectory)../'))" />
```