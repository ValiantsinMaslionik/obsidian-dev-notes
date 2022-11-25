#ASP_NET_CORE/hosting 

---

The _publish_ directory contains the app's deployable assets produced by the [dotnet publish](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-publish) command. The directory contains:

-   Application files
-   Configuration files
-   Static assets
-   Packages
-   A runtime ([self-contained deployment](https://learn.microsoft.com/en-us/dotnet/core/deploying/#self-contained-deployments-scd) only)

#### [Framework-dependent Executable (FDE)](https://learn.microsoft.com/en-us/dotnet/core/deploying/#framework-dependent-executables-fde)

-   **publish†**
    -   `Views`† MVC apps; if views aren't precompiled
    -   `Pages`† MVC or Razor Pages apps, if pages aren't precompiled
    -   `wwwroot`†
    -   `*.dll` files
    -   `{ASSEMBLY NAME}.deps.json`
    -   `{ASSEMBLY NAME}.dll`
    -  `{ASSEMBLY NAME}{.EXTENSION}.exe` extension on Windows, no extension on macOS or Linux
    -   `{ASSEMBLY NAME}.pdb`
    -   `{ASSEMBLY NAME}.runtimeconfig.json`
    -   `web.config` (IIS deployments)
    -   `createdump` ([Linux createdump utility](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))
    -   `*.so` (Linux shared object library)
    -   `*.a` (macOS archive)
    -   `*.dylib` (macOS dynamic library)

#### [Self-contained Deployment (SCD)](https://learn.microsoft.com/en-us/dotnet/core/deploying/#self-contained-deployments-scd)

-   **publish†**
    -   `Views`† MVC apps, if views aren't precompiled
    -   `Pages`† MVC or Razor Pages apps, if pages aren't precompiled
    -   `wwwroot`†
    -   `*.dll` files
    -   `{ASSEMBLY NAME}.deps.json`
    -   `{ASSEMBLY NAME}.dll`
    -   `{ASSEMBLY NAME}{.EXTENSION}` .exe extension on Windows, no extension on macOS or Linux
    -   `{ASSEMBLY NAME}.pdb`
    -   `{ASSEMBLY NAME}.runtimeconfig.json`
    -   `web.config` (IIS deployments)

> `†` Indicates a directory

The `publish` directory represents the _content root path_, also called the _application base path_, of the deployment. Whatever name is given to the _publish_ directory of the deployed app on the server, its location serves as the server's physical path to the hosted app.

The `wwwroot` directory, if present, only contains static assets.