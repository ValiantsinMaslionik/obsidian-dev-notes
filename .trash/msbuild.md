#msbuild

---

#### Packaging

[Shipping a cross-platform MSBuild task in a NuGet package](zDOC_msbuild-Shipping-crossplatform-task-nuget.mhtml)
[Add additional files in nuget package](zDOC_msbuild-additional-files-in-nuget-package.mhtml)

#### SDK style project

[MsBuild condition for new ("sdk-style") vs. old project format](https://stackoverflow.com/questions/52400116/msbuild-condition-for-new-sdk-style-vs-old-project-format)

```xml
<Elenemt Condition="'$(UsingMicrosoftNETSdk)' == 'true'">...</Elenemt>
```

