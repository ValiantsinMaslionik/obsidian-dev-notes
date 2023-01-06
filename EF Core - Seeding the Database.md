#EF_Core 

---

Most applications require some seed data, especially during development. Entity Framework Core does provide a database seeding feature, but it is of limited use for most projects because it doesn’t allow data to be seeded where the database allocates unique keys to the objects it stores. This is an important feature in most data models because it means the application doesn’t have to worry about allocating unique key values.
A more flexible approach is to use the regular Entity Framework Core features to add seed data to the database. Create a file called SeedData.cs in the Platform/Models folder with the code shown in Listing 17-24.

Listing 17-24. The Contents of the SeedData.cs File in the Platform/Models Folder
```cs
using Microsoft.EntityFrameworkCore;

namespace Platform.Models 
{
	public class SeedData 
	{
		private CalculationContext context;
		private ILogger<SeedData> logger;
		
		private static Dictionary<int, long> data = new Dictionary<int, long>() 
		{
			{1, 1}, {2, 3}, {3, 6}, {4, 10}, {5, 15}, {6, 21}, {7, 28}, {8, 36}, {9, 45}, {10, 55}
		};

		public SeedData(CalculationContext dataContext, ILogger<SeedData> log) 
		{
			context = dataContext;
			logger = log;
		}

		public void SeedDatabase() 
		{
			context.Database.Migrate();
			
			if (context.Calculations?.Count() == 0) 
			{
				logger.LogInformation("Preparing to seed database");
				context.Calculations.AddRange(
					data.Select(kvp => new Calculation() 
					{
						Count = kvp.Key, R
						esult = kvp.Value
					}));
				context.SaveChanges();
				logger.LogInformation("Database seeded");
			} 
			else 
			{
				logger.LogInformation("Database not seeded");
			}
		}
	}
}
```

The `SeedData` class declares constructor dependencies on the CalculationContext and `ILogger<T>` types, which are used in the SeedDatabase method to prepare the database. The context’s `Database.Migrate` method is used to apply any pending migrations to the database, and the `Calculations` property is used to store new data using the `AddRange` method, which accepts a sequence of `Calculation` objects.
The new objects are stored in the database using the `SaveChanges` method. To use the `SeedData` class, make the changes shown in Listing 17-25 to the Program.cs file.

Listing 17-25. Enabling Database Seeding in the Program.cs File in the Platform Folder
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

builder.Services.AddTransient<SeedData>();

var app = builder.Build();
app.UseResponseCaching();
app.MapEndpoint<Platform.SumEndpoint>("/sum/{count:int=1000000000}");
app.MapGet("/", async context => 
{
	await context.Response.WriteAsync("Hello World!");
});

bool cmdLineInit = (app.Configuration["INITDB"] ?? "false") == "true";
if (app.Environment.IsDevelopment() || cmdLineInit) 
{
	var seedData = app.Services.GetRequiredService<SeedData>();
	seedData.SeedDatabase();
}

if (!cmdLineInit) 
{
	app.Run();
}
```

I create a service for the `SeedData` class, which means that it will be instantiated, and its dependencies will be resolved, which is more convenient than working directly with the class constructor. 
If the hosting environment is *Development*, the database will be seeded automatically as the application starts. It can also be useful to seed the database explicitly, especially when setting up the application for staging or production testing. This statement checks for a configuration setting named **INITDB**:
```cs
bool cmdLineInit = (app.Configuration["INITDB"] ?? "false") == "true";
```

This setting can be supplied on the command line to seed the database, after which the application will terminate because the `Run` method, which starts listening for HTTP requests, is never called.
To seed the database, open a new PowerShell command prompt, navigate to the project folder, and run the command shown in Listing 17-26.

Listing 17-26. Seeding the Database
```ps
dotnet run INITDB=true
```

The application will start, and the database will be seeded with the results for the ten calculations defined by the `SeedData` class, after which the application will terminate. During the seeding process, you will see the SQL statements that are sent to the database, which check to see whether there are any pending migrations, count the number of rows in the table used to store `Calculation` data, and, if the table is empty, add the seed data.

> If you need to reset the database, you can use the `dotnet ef database drop --force` command. 
> You can then use `dotnet run INITDB=true` to re-create and seed the database again.
