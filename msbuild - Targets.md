#msbuild

---

[Run an MSBuild target once per project instead of once per target framework](zDOC_msbuild-multitargeting-run-target-once-per-project.mhtml) ([Site](https://lizzy-gallagher.github.io/msbuild-run-target-once-per-project/))

[MSBuild targets](https://learn.microsoft.com/en-us/visualstudio/msbuild/msbuild-targets?view=vs-2022)
- [Declare targets in the project file]()
- [Target build order](https://learn.microsoft.com/en-us/visualstudio/msbuild/msbuild-targets?view=vs-2022#target-build-order)
- [SDK-style projects](https://learn.microsoft.com/en-us/visualstudio/msbuild/msbuild-targets?view=vs-2022#sdk-style-projects)
- [Target batching](https://learn.microsoft.com/en-us/visualstudio/msbuild/msbuild-targets?view=vs-2022#target-batching)
```xml
<ItemGroup>
	<Reference Include="System.Core">
		<RequiredTargetFramework>3.5</RequiredTargetFramework>
	</Reference>
	<Reference Include="System.Xml.Linq">
		<RequiredTargetFramework>3.5</RequiredTargetFramework>
	</Reference>
	<Reference Include="Microsoft.CSharp">
		<RequiredTargetFramework>4.0</RequiredTargetFramework>
	</Reference>
</ItemGroup>
<Target Name="AfterBuild" Outputs="%(Reference.RequiredTargetFramework)">
	<Message Text="Reference: @(Reference->'%(RequiredTargetFramework)')" />
</Target>
```
- [Incremental builds](https://learn.microsoft.com/en-us/visualstudio/msbuild/msbuild-targets?view=vs-2022#incremental-builds)
- [Default build targets](https://learn.microsoft.com/en-us/visualstudio/msbuild/msbuild-targets?view=vs-2022#default-build-targets)
[[msbuild - Targets - Standard targets]]