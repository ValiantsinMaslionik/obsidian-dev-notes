#nuget

---


[What is Nuget](https://learn.microsoft.com/en-us/nuget/what-is-nuget)

### Consume packages

#### Reference packages in your projects

[`PackageReference` in project files](https://learn.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files)
- [GeneratePathProperty](https://learn.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files#generatepathproperty)

### Create packages

[Create a package using the nuget.exe CLI](https://learn.microsoft.com/en-us/nuget/create-packages/creating-a-package)
- [From a convention-based working directory](https://learn.microsoft.com/en-us/nuget/create-packages/creating-a-package#from-a-convention-based-working-directory)

[Package authoring best practices](https://learn.microsoft.com/en-us/nuget/create-packages/package-authoring-best-practices)

#### Advanced tasks

[Supporting multiple target frameworks](https://learn.microsoft.com/en-us/nuget/create-packages/supporting-multiple-target-frameworks)
[Transforming source code and configuration files](https://learn.microsoft.com/en-us/nuget/create-packages/source-and-config-file-transformations)

### Concepts

[Package installation process](https://learn.microsoft.com/en-us/nuget/concepts/package-installation-process)
[Package versioning](https://learn.microsoft.com/en-us/nuget/concepts/package-versioning)

Notation|Applied rule|Description
--|--|--
1.0|x \≥ 1.0|Minimum version, inclusive
(1.0,)|x \> 1.0|Minimum version, exclusive
\[1.0\]|x == 1.0|Exact version match
(,1.0\]|x \≤ 1.0|Maximum version, inclusive
(,1.0)|x \< 1.0|Maximum version, exclusive
\[1.0,2.0\]|1.0 \≤ x \≤ 2.0|Exact range, inclusive
(1.0,2.0)|1.0 \< x \< 2.0|Exact range, exclusive
\[1.0,2.0)|1.0 \≤ x \< 2.0|Mixed inclusive minimum and exclusive maximum version
(1.0)|invalid|invalid

[MSBuild .props and .targets in a package](https://learn.microsoft.com/en-us/nuget/concepts/msbuild-props-and-targets?source=recommendations)
- [buildTransitive](https://github.com/NuGet/Home/wiki/Allow-package--authors-to-define-build-assets-transitive-behavior)

### Reference

[.nuspec](https://learn.microsoft.com/en-us/nuget/reference/nuspec)
[NuGet pack and restore as MSBuild targets](https://learn.microsoft.com/en-us/nuget/reference/msbuild-targets)
- [Including content in a package](https://learn.microsoft.com/en-us/nuget/reference/msbuild-targets#including-content-in-a-package)