#aspnet_core/Caching 

---

The cache created by the `AddDistributedMemoryCache` method isn’t distributed, despite the name. The items are stored in memory as part of the ASP.NET Core process, which means that applications that run on multiple servers or containers don’t share cached data. It also means that the contents of the cache are lost when ASP.NET Core is stopped. 
The `AddDistributedSqlServerCache` method stores the cache data in a SQL Server database, which can be shared between multiple ASP.NET Core servers and which stores the data persistently. The first step is to create a database that will be used to store the cached data. You can store the cached data alongside the application’s other data, but for this chapter, I am going to use a separate database, which will be named `CacheDb`. You can create the database using Azure Data Studio or SQL Server Management Studio, both of which are available for free from Microsoft. Databases can also be created from the command line using `sqlcmd`. Open a new PowerShell command prompt and run the command shown in Listing 17-7 to connect to the LocalDB server.

> The `sqlcmd` tool should have been installed as part of the Visual Studio workload or as part of the SQL Server Express installation. 
> If it has not been installed, then you can download an installer from https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility?view=sql-server-ver15.

Listing 17-7. Connecting to the Database
`sqlcmd -S "(localdb)\MSSQLLocalDB"`

Pay close attention to the argument that specifies the database. There is one backslash, which is followed by *MSSQLLocalDB*. It can be hard to spot the repeated letters: MS-SQL-LocalDB (but without the hyphens).
When the connection has been established, you will see a *1>* prompt. Enter the commands shown in Listing 17-8 and press the Enter key after each command.

> ![[zICO - Warning - 16.png]] If you are using Visual Studio, you must apply the updates for SQL Server described in Chapter 2. The version of SQL Server that is installed by default when you install Visual Studio cannot create LocalDB databases.

Listing 17-8. Creating the Database
```sql
CREATE DATABASE CacheDb
GO
EXIT
```

The next step is to run the command shown in Listing 17-9 to create a table in the new database, which uses a global .NET Core tool to prepare the database.

> If you need to reset the cache database, use the command in Listing 17-7 to open a connection and use the command `DROP DATABASE CacheDB`. 
> You can then re-create the database using the commands in Listing 17-8.

Listing 17-9. Creating the Cache Database Table
`dotnet sql-cache create "Server=(localdb)\MSSQLLocalDB;Database=CacheDb" dbo DataCache`

The arguments for this command are the connection string that specifies the database, the schema, and the name of the table that will be used to store the cached data. Enter the command on a single line and press Enter. It will take a few seconds for the tool to connect to the database. If the process is successful, you will see the following message:
`Table and index were created successfully.`

## Creating the Persistent Cache Service

Now that the database is ready, I can create the service that will use it to store cached data. To add the NuGet package required for SQL Server caching support, open a new PowerShell command prompt, navigate to the Platform project folder, and run the command shown in Listing 17-10. (If you are using Visual Studio, you can add the package by selecting Project ➤ Manage NuGet Packages.)

Listing 17-10. Adding a Package to the Project
`dotnet add package Microsoft.Extensions.Caching.SqlServer --version 6.0.0`

The next step is to define a connection string, which describes the database connection in the JSON configuration file, as shown in Listing 17-11.

> The cache created by the `AddDistributedSqlServerCache` method is distributed, meaning that multiple applications can use the same database and share cache data. If you are deploying the same application to multiple servers or containers, all instances will be able to share cached data. If you are sharing a cache between different applications, then you should pay close attention to the keys you use to ensure that applications receive the data types they expect.

Listing 17-11. Defining a Connection String in the appsettings.json File in the Platform Folder
```json
{
	"Logging": {
		"LogLevel": {
			"Default": "Information",
			"Microsoft.AspNetCore": "Warning"
		}
	},
	"AllowedHosts": "*",
	"Location": {
		"CityName": "Buffalo"
	},
	"ConnectionStrings": {
		"CacheConnection": "Server=(localdb)\\MSSQLLocalDB;Database=CacheDb"
	}
}
```

Notice that the connection string uses two backslash characters (\\) to escape the character in the JSON file. Listing 17-12 changes the implementation for the cache service to use SQL Server with the connection string from Listing 17-11.

Listing 17-12. Using a Persistent Data Cache in the Program.cs File in the Platform Folder
```cs
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddDistributedSqlServerCache(opts => 
{
	opts.ConnectionString = builder.Configuration["ConnectionStrings:CacheConnection"];
	opts.SchemaName = "dbo";
	opts.TableName = "DataCache";
});

var app = builder.Build();
app.MapEndpoint<Platform.SumEndpoint>("/sum/{count:int=1000000000}");
app.MapGet("/", async context => 
{
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```

The `IConfiguration` service is used to access the connection string from the application’s configuration data. The cache service is created using the `AddDistributedSqlServerCache` method and is configured using an instance of the `SqlServerCacheOptions` class, whose most useful properties are described in Table 17-7.

Table 17-7. Useful SqlServerCacheOptions Properties

Name|Description
--|--
ConnectionString|This property specifies the connection string, which is conventionally stored in the JSON configuration file and accessed through the `IConfguration` service.
SchemaName|This property specifies the schema name for the cache table.
TableName|This property specifies the name of the cache table.
ExpiredItemsDeletionInterval|This property specifies how often the table is scanned for expired items. The default is 30 minutes.
DefaultSlidingExpiration|This property specifies how long an item remains unread in the cache before it expires. The default is 20 minutes.

### ![[zICO - Warning - 16.png]] Caching Session-Specific Data Values

When you use the `IDistributedCache` service, the data values are shared between all requests. If you want to cache different data values for each user, then you can use the session middleware described in Chapter 16. The session middleware relies on the `IDistributedCache` service to store its data, which means that session data will be stored persistently and be available to a distributed application when the `AddDistributedSqlServerCache` method is used.

## Caching Responses

An alternative to caching individual data items is to cache entire responses, which can be a useful approach if a response is expensive to compose and is likely to be repeated. Caching responses requires the addition of a service and a middleware component, as shown in Listing 17-13.

Listing 17-13. Configuring Response Caching in the Program.cs File in the Platform Folder
```cs
using Platform.Services;
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddDistributedSqlServerCache(opts => 
{
	opts.ConnectionString = builder.Configuration["ConnectionStrings:CacheConnection"];
	opts.SchemaName = "dbo";
	opts.TableName = "DataCache";
});

builder.Services.AddResponseCaching();
builder.Services.AddSingleton<IResponseFormatter, HtmlResponseFormatter>();

var app = builder.Build();
app.UseResponseCaching();
app.MapEndpoint<Platform.SumEndpoint>("/sum/{count:int=1000000000}");

app.MapGet("/", async context => 
{
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```

The `AddResponseCaching` method is used to set up the service used by the cache. The middleware component is added with the `UseResponseCaching` method, which should be called before any endpoint or middleware that needs its responses cached. I have also defined the `IResponseFormatter` service, which I used to explain how dependency injection works in Chapter 14. Response caching is used only in certain circumstances, and, as I explain shortly, demonstrating the feature requires an HTML response.

> ![[zICO - Warning - 16.png]] The response caching feature does not use the `IDistributedCache` service. **Responses are cached in memory and are not distributed**.

In Listing 17-14, I have updated the SumEndpoint class so that it requests response caching instead of caching just a data value.

Listing 17-14. Using Response Caching in the SumEndpoint.cs File in the Platform Folder using Microsoft.Extensions.Caching.Distributed;
```cs
using Platform.Services;

namespace Platform 
{
	public class SumEndpoint 
	{
		public async Task Endpoint(HttpContext context, IDistributedCache cache, IResponseFormatter formatter, LinkGenerator generator) 
		{
			int count;
			int.TryParse((string?)context.Request.RouteValues["count"], out count);
			long total = 0;
			
			for (int i = 1; i <= count; i++) 
			{
				total += i;
			}
			
			string totalString = $"({ DateTime.Now.ToLongTimeString() }) {total}";
			context.Response.Headers["Cache-Control"] = "public, max-age=120";
			
			string? url = generator.GetPathByRouteValues(context, null, new { count = count });
			await formatter.Format(context, $"<div>({DateTime.Now.ToLongTimeString()}) Total for {count}" + $" values:</div><div>{totalString}</div>" + $"<a href={url}>Reload</a>");
		}
	}
}
```

Some of the changes to the endpoint enable response caching, but others are just to demonstrate that it is working. 
**For enabling response caching, the important statement is the one that adds a header to the response, like this:**
```cs
context.Response.Headers["Cache-Control"] = "public, max-age=120";
```

The *Cache-Control* header is used to control response caching. The middleware will only cache responses that have a *Cache-Control* header that contains the *public* directive. The *max-age* directive is used to specify the period that the response can be cached for, expressed in seconds. The *Cache-Control* header used in Listing 17-14 enables caching and specifies that responses can be cached for two minutes. 

> ![[zICO - Warning - 16.png]] Enabling response caching is simple, **but checking that it is working requires care**. When you reload the browser window or press Return in the URL bar, browsers will include a *Cache-Control* header in the request that sets the max-age directive to zero, which bypasses the response cache and causes a new response to be generated by the endpoint. The only reliable way to request a URL without the *Cache-Control* header is to navigate using an HTML anchor element, which is why the endpoint in Listing 17-14 uses the `IResponseFormatter` service to generate an HTML response and uses the `LinkGenerator` service to create a URL that can be used in the anchor element’s href attribute.

To check the response cache, restart ASP.NET Core and use the browser to request http://localhost:5000/sum. Once the response has been generated, click the *Reload* link to request the same URL. You will see that neither of the timestamps in the response change, indicating that the entire response has been cached, as shown in Figure 17-4.

![[zIMG-ASP.NET-caching-17-4.png]]

> The *Cache-Control* header can be combined with the *Vary* header to provide fine-grained control over which requests are cached. See https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control and https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Vary for details of the features provided by both headers.

### Compressing Responses

ASP.NET Core includes middleware that will compress responses for browsers that have indicated they can handle compressed data. The middleware is added to the pipeline with the `UseResponseCompression` method. Compression is a trade-off between the server resources
required for compression and the bandwidth required to deliver content to the client, and it should not be switched on without testing to determine the performance impact.

