#EF_Core 

---

Access to the database is provided through a service, as shown in Listing 17-20.

Listing 17-20. Configuring the Data Service in the Program.cs File in the Platform Folder
```cs
using Platform.Services;
using Platform.Models;
using Microsoft.EntityFrameworkCore;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDistributedSqlServerCache(opts => 
{
	opts.ConnectionString = builder.Configuration["ConnectionStrings:CacheConnection"];
	opts.SchemaName = "dbo";
	opts.TableName = "DataCache";
});

builder.Services.AddResponseCaching();
builder.Services.AddSingleton<IResponseFormatter, HtmlResponseFormatter>();
builder.Services.AddDbContext<CalculationContext>(opts => 
{
	opts.UseSqlServer(builder.Configuration["ConnectionStrings:CalcConnection"]);
});

var app = builder.Build();
app.UseResponseCaching();
app.MapEndpoint<Platform.SumEndpoint>("/sum/{count:int=1000000000}");

app.MapGet("/", async context => 
{
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```

The `AddDbContext` method creates a service for an Entity Framework Core context class. The method receives an options object that is used to select the database provider, which is done with the `UseSqlServer` method. 
The `IConfiguration` service is used to get the connection string for the database, which is defined in Listing 17-21.

Listing 17-21. Defining a Connection String in the appsettings.json File in the Platform Folder
```json
{
	"Logging": {
		"LogLevel": {
			"Default": "Information",
			"Microsoft.AspNetCore": "Warning",
			"Microsoft.EntityFrameworkCore": "Information"
		}
	},
	"AllowedHosts": "*",
	"Location": {
		"CityName": "Buffalo"
	},
	"ConnectionStrings": {
		"CacheConnection": "Server=(localdb)\\MSSQLLocalDB;Database=CacheDb",
		"CalcConnection": "Server=(localdb)\\MSSQLLocalDB;Database=CalcDb"
	}
}
```

The listing also sets the logging level for the Microsoft.EntityFrameworkCore category, which will show the SQL statements that are used by Entity Framework Core to query the database.

> ![[zICO - Warning - 16.png]] Set the `MultipleActiveResultSets` option to `True` for connection strings that will be used to make queries with multiple result sets.
