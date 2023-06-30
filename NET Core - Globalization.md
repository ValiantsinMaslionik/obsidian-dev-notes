#netcore/globalization

---

https://learn.microsoft.com/en-us/dotnet/core/extensions/globalization-icu

## Using ICU library in .NET apps

### ICU on WIndows

https://learn.microsoft.com/en-us/dotnet/core/extensions/globalization-icu#icu-on-windows

```xml
...
  <ItemGroup>
    <!-- Reference to icu4c library fork (MS-ICU) provided by Microsoft -->
    <!-- https://github.com/microsoft/icu -->
    <PackageReference Include="Microsoft.ICU.ICU4C.Runtime" Version="68.2.0.9" />
    <!-- Force using app-local ICU library to avoid platform dependencies -->
    <RuntimeHostConfigurationOption Include="System.Globalization.AppLocalIcu" Value="68" />
    <!-- Force using NLS -->
    <!-- <RuntimeHostConfigurationOption Include="System.Globalization.UseNls" Value="true" /> -->
  </ItemGroup>

```

```cs
public static bool ICUMode()
{
	SortVersion sortVersion = CultureInfo.InvariantCulture.CompareInfo.Version; 
	byte[] bytes = sortVersion.SortId.ToByteArray(); 
	int version = bytes[3] << 24 | bytes[2] << 16 | bytes[1] << 8 | bytes[0]; return version != 0 && version == sortVersion.FullVersion; 
}
```