#net/globalization

---

https://learn.microsoft.com/en-us/dotnet/core/extensions/globalization-and-localization

## Globalization

https://learn.microsoft.com/en-us/dotnet/core/extensions/globalization

## Globalization and ICU

https://learn.microsoft.com/en-us/dotnet/core/extensions/globalization-icu

Windows 10 May 2019 Update and later versions include [icu.dll](https://learn.microsoft.com/en-us/windows/win32/intl/international-components-for-unicode--icu-) as part of the OS, and .NET 5 and later versions use ICU by default. When running on Windows, .NET 5 and later versions try to load `icu.dll` and, if it's available, use it for the globalization implementation. If the ICU library can't be found or loaded, such as when running on older versions of Windows, .NET 5 and later versions fall back to the NLS-based implementation.

> Even when using ICU, the `CurrentCulture`, `CurrentUICulture`, and `CurrentRegion` members still use Windows operating system APIs to honor user settings.

### Determine if your app is using ICU

https://learn.microsoft.com/en-us/dotnet/core/extensions/globalization-icu#determine-if-your-app-is-using-icu

The following code snippet can help you determine if your app is running with ICU libraries (and not NLS).

```cs
public static bool ICUMode()
{
    SortVersion sortVersion = CultureInfo.InvariantCulture.CompareInfo.Version;
    byte[] bytes = sortVersion.SortId.ToByteArray();
    int version = bytes[3] << 24 | bytes[2] << 16 | bytes[1] << 8 | bytes[0];
    return version != 0 && version == sortVersion.FullVersion;
}
```

### App-local ICU

https://learn.microsoft.com/en-us/dotnet/core/extensions/globalization-icu#app-local-icu

Each release of ICU may bring with it bug fixes as well as updated Common Locale Data Repository (CLDR) data that describes the world's languages. Moving between versions of ICU can subtly impact app behavior when it comes to globalization-related operations. To help application developers ensure consistency across all deployments, .NET 5 and later versions enable apps on both Windows and Unix to carry and use their own copy of ICU.