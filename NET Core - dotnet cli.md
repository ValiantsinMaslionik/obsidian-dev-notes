#NET_CORE/SDK/cli

---

[[#Building and Running Projects]]
[[#Create project and solution]]
[[#Managing Packages]]
[[#Managing Tool Packages]]
[[#Utility]]

## Building and Running Projects

- building
`dotnet build [<project_file>]`
- running project
`dotnet run [<project_file>]`
- Hot Reload Feature
As you make changes to the project, you will see that the code is automatically recompiled and that changes are automatically displayed in the browser.
`dotnet watch [<project_name>]`
- running application (dll)
`dotnet <dll_name>`
- running test project
`dotnet test [<project_name>]`

## Create project and solution
- Asp.Net Core
```
dotnet new globaljson --sdk-version 6.0.100 --output SportsSln/SportsStore
dotnet new web --no-https --output SportsSln/SportsStore --framework net6.0
dotnet new sln -o SportsSln
dotnet sln SportsSln add SportsSln/SportsStore
```
- Asp.Net Core MVC
```
dotnet new globaljson --sdk-version 6.0.100 --output FirstProject
dotnet new mvc --no-https --output FirstProject --framework net6.0
dotnet new sln -o FirstProject
dotnet sln FirstProject add FirstProject
```
- Test
```
dotnet new xunit -o SimpleApp.Tests --framework net6.0
dotnet sln add SimpleApp.Tests
dotnet add SimpleApp.Tests reference SimpleApp
```
| Name | Description
---|---
mstest | This template creates a project configured for the MS Test framework, which is produced by Microsoft.
nunit | This template creates a project configured for the NUnit framework.
xunit | This template creates a project configured for the XUnit framework.

## Managing Packages

- Adding a package
`dotnet add package Swashbuckle.AspNetCore --version 6.2.2`
- Listing the Packages in a Project
`dotnet list package`
- Removing a Package
`dotnet remove package Microsoft.EntityFrameworkCore.SqlServer`

## Managing Tool Packages

Tool packages install commands that can be used from the command line to perform operations on .NET projects. 

One common example is the Entity Framework Core tools package that installs commands that
are used to manage databases in ASP.NET Core projects.
```
dotnet tool uninstall --global dotnet-ef
dotnet tool install --global dotnet-ef --version 6.0.0
```

> The --global arguments in Listing 4-14 mean the package is installed for global use and not just for a specific project.

### Running a Tool Package Command
`dotnet ef --help`

### Managing Client-Side Packages

Client-side packages contain content that is delivered to the client, such as images, CSS stylesheets,
JavaScript files, and static HTML. Client-side packages are added to ASP.NET Core using the Library
Manager (LibMan) tool.
```
dotnet tool uninstall --global Microsoft.Web.LibraryManager.Cli
dotnet tool install --global Microsoft.Web.LibraryManager.Cli --version 2.1.113
```

The next step is to initialize the project, which creates the file that LibMan uses to keep track of the
client packages it installs.
`libman init -p cdnjs`

## Utility

- Listing the Installed SDKs
`dotnet --list-sdks`



