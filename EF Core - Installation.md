#EF_Core 

---

## ![[zICO - Warning - 16.png]] WORKING WITH ENTITY FRAMEWORK CORE

The most common complaint about Entity Framework Core is poor performance. When I review projects that have Entity Framework Core performance issues, the problem is almost always because the development team has treated Entity Framework Core as a black box and not paid attention to the SQL queries that are sent to the database. Not all LINQ features can be translated into SQL, and the most common problem is a query that retrieves large amounts of data from the database, which is then discarded after it has been reduced to produce a single value.

Using Entity Framework Core requires a good understanding of SQL and ensuring that the LINQ queries made by the application are translated into efficient SQL queries. There are rare applications that have high-performance data requirements that cannot be met by Entity Framework Core, but that isn’t the case for most typical web applications.

That is not to say that Entity Framework Core is perfect. It has its quirks and requires an investment in time to become proficient. If you don’t like the way that Entity Framework Core works, then you may prefer to use an alternative, such as Dapper (https://dapperlib.github.io/Dapper). But if your issue is that queries are not being performed fast enough, then you should spend some time exploring how those queries are being processed, which you can do using the techniques described in the remainder of the chapter.

## Installing Entity Framework Core

Entity Framework Core requires a global tool package that is used to manage databases from the command line and to manage packages for the project that provide data access. To install the tools package, open a new PowerShell command prompt and run the commands shown in Listing 17-15.

Listing 17-15. Installing the Entity Framework Core Global Tool Package
```ps
dotnet tool uninstall --global dotnet-ef
dotnet tool install --global dotnet-ef --version 6.0.0
```

The first command removes any existing version of the *dotnet-ef* package, and the second command installs the version required for the examples in this book. This package provides the *dotnet ef* commands that you will see in later examples. To ensure the package is working as expected, run the command shown in Listing 17-16.

Listing 17-16. Testing the Entity Framework Core Global Tool
```ps
dotnet ef --help
```

This command shows the help message for the global tool and produces the following output:
```txt
Entity Framework Core .NET Command-line Tools 6.0.0
Usage: dotnet ef [options] [command]
Options:
--version Show version information
-h|--help Show help information
-v|--verbose Show verbose output.
--no-color Don't colorize output.
--prefix-output Prefix output with level.
Commands:
database Commands to manage the database.
dbcontext Commands to manage DbContext types.
migrations Commands to manage migrations.
Use "dotnet ef [command] --help" for more information about a command.
```

Entity Framework Core also requires packages to be added to the project. If you are using Visual Studio Code or prefer working from the command line, navigate to the Platform project folder (the folder that contains the Platform.csproj file) and run the commands shown in Listing 17-17.

Listing 17-17. Adding Entity Framework Core Packages to the Project
```ps
dotnet add package Microsoft.EntityFrameworkCore.Design --version 6.0.0
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 6.0.0
```