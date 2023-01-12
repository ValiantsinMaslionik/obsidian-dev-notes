#msbuild 

---

### Batching

https://learn.microsoft.com/en-us/visualstudio/msbuild/msbuild-batching?view=vs-2022
- [Task batching](https://learn.microsoft.com/en-us/visualstudio/msbuild/msbuild-batching?view=vs-2022#task-batching)
- [Item metadata in task batching](https://learn.microsoft.com/en-us/visualstudio/msbuild/item-metadata-in-task-batching?view=vs-2022)
- [Item metadata in target batching](https://learn.microsoft.com/en-us/visualstudio/msbuild/item-metadata-in-target-batching?view=vs-2022)

### Customize your build

https://learn.microsoft.com/en-us/visualstudio/msbuild/customize-your-build?view=vs-2022

How to import parent Directory.Build.props:

```xml
<Import Project="$([MSBuild]::GetPathOfFileAbove('Directory.Build.props', '$(MSBuildThisFileDirectory)../'))" />
```