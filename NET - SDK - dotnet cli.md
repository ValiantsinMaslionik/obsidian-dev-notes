#NET/SDK

---

## Building and Running Projects

Command|Description
--|--
dotnet build \[\<project_name\>\]|Compiles the project
dotnet test \[\<project_name\>\]|Runs unit tests on the project
dotnet run \[\<project_name\>\]|Runs the project
dotnet pack \[\<project_name\>\]|Creates NuGEt package for the project
dotnet publish \[\<project_name\>\]|Compiles and then publishes the project, either with dependencies or as a self-contained application
dotnet \[\<app_name.dll\>\]|Runs the app

## Create project and solution

### To list currently installed templates

```ps1
dotnet new -l
```

### Console
```ps1
dotnet new console
```

### Asp.Net Core

```ps1
dotnet new globaljson --sdk-version 6.0.100 --output SportsSln/SportsStore
dotnet new web --no-https --output SportsSln/SportsStore --framework net6.0
dotnet new sln -o SportsSln
dotnet sln SportsSln add SportsSln/SportsStore
```

### Asp.Net Core MVC

```ps1
dotnet new globaljson --sdk-version 6.0.100 --output FirstProject
dotnet new mvc --no-https --output FirstProject --framework net6.0
dotnet new sln -o FirstProject
dotnet sln FirstProject add FirstProject
```

### Test

```powershell
dotnet new xunit -o SimpleApp.Tests --framework net6.0
dotnet sln add SimpleApp.Tests
dotnet add SimpleApp.Tests reference SimpleApp
```

 Name | Description
--|--
mstest | This template creates a project configured for the MS Test framework, which is produced by Microsoft.
nunit | This template creates a project configured for the NUnit framework.
xunit | This template creates a project configured for the XUnit framework.

## Managing Packages

Command|Description
--|--
dotnet add package &lt;id&gt; --version &lt;x.x.x&gt;|Adding a package to a project
dotnet list package|Listing the packages in a Project
dotnet remove package &lt;id&gt;|Removing the package from a project
dotnet restore|To restore packages in a project (or solution)

## Managing Tool Packages

Tool packages install commands that can be used from the command line to perform operations on .NET projects. 

One common example is the Entity Framework Core tools package that installs commands that
are used to manage databases in ASP.NET Core projects.

```powershell
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

```ps1
dotnet tool uninstall --global Microsoft.Web.LibraryManager.Cli
dotnet tool install --global Microsoft.Web.LibraryManager.Cli --version 2.1.113
```

The next step is to initialize the project, which creates the file that LibMan uses to keep track of the
client packages it installs.

`libman init -p cdnjs`

## Utility

### Get available .NET version (that is installed locally)
`dotnet --version`

### Listing the Installed SDKs

`dotnet --list-sdks`

### Listing the Installed Runtimes

`dotnet --list-runtimes`

### Uninstalling old and preview versions

`dotnet-core-uninstall --all-previews-but-latest --sdk`

### Help na d documentation

- To open official documentation in a browser window
`dotnet help new`

- To get hlp oputput at the command line, use -h or --help flag
`dotnet new console -h`

- Search in Google
`garbage collection site:stackoverflow.com +C# -Java`


