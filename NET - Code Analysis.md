#net/codeanalysis

---

https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/overview?tabs=net-7

## Configuration

### Configuration files

https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/configuration-files

AlayzerConfig example
```ini
is_global=true
#global_level=100

# VisualStudio buil-it code style rules ------------------------------
dotnet_diagnostic.IDE0001.severity = warning
dotnet_diagnostic.IDE0002.severity = warning
```

How to inlude in a project
```xml
<ItemGroup Label="CodeAnalyzerRules" >
	<GlobalAnalyzerConfigFiles Include="p:\NetAnalyserRules.globalconfig" />
</ItemGroup>
```

Both EditorConfig files and global AnalyzerConfig files specify a key-value pair for each option. Conflicts arise when there are multiple entries with the same key but different values. The following precedence rules are used to resolve conflicts.

Conflicting entry locations|Precedence rule
--|--
In the same configuration file|The entry that appears later in the file wins. This is true for conflicting entries within a single EditorConfig file and also within a single global AnalyzerConfig file.e system, and hence has a longer file path, wins.
In two EditorConfig files|The entry in the EditorConfig file that's deeper in the fil
In two global AnalyzerConfig files|**.NET 5**: A compiler warning is reported and both entries are ignored.<br>**.NET 6 and later versions**: The entry from the file with a higher value for `global_level` takes precedence. If `global_level` isn't explicitly defined and the file is named _.globalconfig_, the `global_level` value defaults to `100`; for all other global AnalyzerConfig files, `global_level` defaults to `0`. If the `global_level` values for the configuration files with conflicting entries are equal, a compiler warning is reported and both entries are ignored.
In an EditorConfig file and a Global AnalyzerConfig file|The entry in the EditorConfig file wins.

[](https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/configuration-files#severity-options)
