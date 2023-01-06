#EF_Core/asp_net_core 

---

## Creating the Project

Open a new PowerShell command prompt and run the commands shown in Listing 18-1.

Listing 18-1. Creating the Project
```ps
dotnet new globaljson --sdk-version 6.0.100 --output WebApp
dotnet new web --no-https --output WebApp --framework net6.0
dotnet new sln -o WebApp
dotnet sln WebApp add WebApp
```

If you are using Visual Studio, open the WebApp.sln file in the WebApp folder. If you are using Visual Studio Code, open the WebApp folder. Click the Yes button when prompted to add the assets required for building and debugging the project, as shown in Figure 18-1.

Open the *launchSettings.json* file in the *WebApp/Properties* folder, change the HTTP port, and disable automatic browser launching, as shown in Listing 18-2.

Listing 18-2. Setting the HTTP Port in the launchSettings.json File in the Properties Folder
```json
{
	"iisSettings": {
		"windowsAuthentication": false,
		"anonymousAuthentication": true,
		"iisExpress": {
			"applicationUrl": "http://localhost:5000",
			"sslPort": 0
		}
	},
	"profiles": {
		"WebApp": {
			"commandName": "Project",
			"dotnetRunMessages": true,
			"launchBrowser": false,
			"applicationUrl": "http://localhost:5000",
			"environmentVariables": {
				"ASPNETCORE_ENVIRONMENT": "Development"
			}
		},
		"IIS Express": {
			"commandName": "IISExpress",
			"launchBrowser": true,
			"environmentVariables": {
				"ASPNETCORE_ENVIRONMENT": "Development"
			}
		}
	}
}
```

## Adding a Data Model

A data model helps demonstrate the different ways that web applications can be built using ASP.NET Core, showing how complex responses can be composed and how data can be submitted by the user. In the sections that follow, I create a simple data model and use it to create the database schema that will be used to store the application’s data.

### Adding NuGet Packages to the Project

The data model will use Entity Framework Core to store and query data in a SQL Server LocalDB database. To add the NuGet packages for Entity Framework Core, use a PowerShell command prompt to run the commands shown in Listing 18-3 in the WebApp project folder.

Listing 18-3. Adding Packages to the Project
```
dotnet add package Microsoft.EntityFrameworkCore.Design --version 6.0.0
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 6.0.0
```

If you are using Visual Studio, you can add the packages by selecting Project ➤ Manage NuGet Packages. Take care to choose the correct version of the packages to add to the project.

If you have not followed the examples in earlier chapters, you will need to install the global tool package that is used to create and manage Entity Framework Core migrations. Run the commands shown in Listing 18-4 to remove any existing version of the package and install the version required for this book. (You can skip these commands if you installed this version of the tools package in earlier chapters.)

Listing 18-4. Installing a Global Tool Package
```ps
dotnet tool uninstall --global dotnet-ef
dotnet tool install --global dotnet-ef --version 6.0.0
```

### Creating the Data Model

The data model for this part of the book will consist of three related classes: `Product`, `Supplier`, and `Category`. Create a new folder named *Models* and add to it a class file named Category.cs, with the contents shown in Listing 18-5.

Listing 18-5. The Contents of the Category.cs File in the Models Folder
```cs
namespace WebApp.Models 
{
	public class Category 
	{
		public long CategoryId { get; set; }
		public string Name { get; set; } = string.Empty;
		public IEnumerable<Product>? Products { get; set; }
	}
}
```

Add a class called Supplier.cs to the Models folder and use it to define the class shown in Listing 18-6.

Listing 18-6. The Contents of the Supplier.cs File in the Models Folder
```cs
namespace WebApp.Models 
{
	public class Supplier 
	{
		public long SupplierId { get; set; }
		public string Name { get; set; } = string.Empty;
		public string City { get; set; } = string.Empty;
		public IEnumerable<Product>? Products { get; set; }
	}
}
```

Next, add a class named Product.cs to the Models folder and use it to define the class shown in Listing 18-7.

Listing 18-7. The Contents of the Product.cs File in the Models Folder
```cs
using System.ComponentModel.DataAnnotations.Schema;

namespace WebApp.Models 
{
	public class Product 
	{
		public long ProductId { get; set; }
		public string Name { get; set; } = string.Empty;
		[Column(TypeName = "decimal(8, 2)")]
		public decimal Price { get; set; }
		public long CategoryId { get; set; }
		public Category? Category { get; set; }
		public long SupplierId { get; set; }
		public Supplier? Supplier { get; set; }
	}
}
```

Each of the three data model classes defines a key property whose value will be allocated by the database when new objects are stored. There are also navigation properties that will be used to query for related data so that it will be possible, for example, to query for all the products in a specific category.

The `Price` property has been decorated with the `Column` attribute, which specifies the precision of the values that will be stored in the database. There isn’t a one-to-one mapping between C# and SQL numeric types, and the `Column` attribute tells Entity Framework Core which SQL type should be used in the database to store `Price` values. In this case, the `decimal(8, 2)` type will allow a total of eight digits, including two following the decimal point.

To create the Entity Framework Core context class that will provide access to the database, add a file called *DataContext.cs* to the *Models* folder and add the code shown in Listing 18-8.

Listing 18-8. The Contents of the DataContext.cs File in the Models Folder
```cs
using Microsoft.EntityFrameworkCore;

namespace WebApp.Models 
{
	public class DataContext : DbContext 
	{
		public DataContext(DbContextOptions<DataContext> opts)
			: base(opts) 
		{ }
		
		public DbSet<Product> Products => Set<Product>();
		public DbSet<Category> Categories => Set<Category>();
		public DbSet<Supplier> Suppliers => Set<Supplier>();
	}
}
```

The context class defines properties that will be used to query the database for `Product`, `Category`, and `Supplier` data.

### Preparing the Seed Data

Add a class called *SeedData.cs* to the *Models* folder and add the code shown in Listing 18-9 to define the seed data that will be used to populate the database.

Listing 18-9. The Contents of the SeedData.cs File in the Models Folder
```cs
using Microsoft.EntityFrameworkCore;

namespace WebApp.Models 
{
	public static class SeedData 
	{
		public static void SeedDatabase(DataContext context) 
		{
			context.Database.Migrate();
			
			if (context.Products.Count() == 0 && context.Suppliers.Count() == 0 && context.Categories.Count() == 0) 
			{
				Supplier s1 = new Supplier { Name = "Splash Dudes", City = "San Jose"};
				Supplier s2 = new Supplier { Name = "Soccer Town", City = "Chicago"};
				Supplier s3 = new Supplier { Name = "Chess Co", City = "New York"};
				
				Category c1 = new Category { Name = "Watersports" };
				Category c2 = new Category { Name = "Soccer" };
				Category c3 = new Category { Name = "Chess" };
				
				context.Products.AddRange(
					new Product { Name = "Kayak", Price = 275,	Category = c1, Supplier = s1},
					new Product { Name = "Lifejacket", Price = 48.95m, Category = c1, Supplier = s1},
					new Product { Name = "Soccer Ball", Price = 19.50m, Category = c2, Supplier = s2},
					new Product { Name = "Corner Flags", Price = 34.95m, Category = c2, Supplier = s2},
					new Product { Name = "Stadium", Price = 79500, Category = c2, Supplier = s2},
					new Product { Name = "Thinking Cap", Price = 16, Category = c3, Supplier = s3},
					new Product { Name = "Unsteady Chair", Price = 29.95m, Category = c3, Supplier = s3},
					new Product { Name = "Human Chess Board", Price = 75, Category = c3, Supplier = s3},
					new Product { Name = "Bling-Bling King", Price = 1200, Category = c3, Supplier = s3}
				);
				
				context.SaveChanges();
			}
		}
	}
}
```

The static `SeedDatabase` method ensures that all pending migrations have been applied to the database. If the database is empty, it is seeded with categories, suppliers, and products. Entity Framework Core will take care of mapping the objects into the tables in the database, and the key properties will be assigned automatically when the data is stored.

## Configuring Entity Framework Core Services and Middleware

Make the changes to the Program.cs file shown in Listing 18-10, which configure Entity Framework Core and set up the `DataContext` services that will be used throughout this part of the book to access the database.

Listing 18-10. Preparing Services and Middleware in the Program.cs File in the WebApp Folder
```cs
using Microsoft.EntityFrameworkCore;
using WebApp.Models;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<DataContext>(opts => 
{
	opts.UseSqlServer(builder.Configuration["ConnectionStrings:ProductConnection"]);
	opts.EnableSensitiveDataLogging(true);
});

var app = builder.Build();

app.MapGet("/", () => "Hello World!");

var context = app.Services.CreateScope().ServiceProvider.GetRequiredService<DataContext>();
SeedData.SeedDatabase(context);

app.Run();
```

> The `DataContext` service is scoped, which means that I have to create a scope to get the service required by the `SeedDatabase` method.

To define the connection string that will be used for the application’s data, add the configuration settings shown in Listing 18-11 in the appsettings.json file. The connection string should be entered on a single line.

Listing 18-11. Defining a Connection String in the appsettings.json File in the WebApp Folder
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
	"ConnectionStrings": {
		"ProductConnection": "Server=(localdb)\\MSSQLLocalDB;Database=Products;MultipleActiveResultSets=True"
	}
}
```

In addition to the connection string, Listing 18-11 sets the logging detail for Entity Framework Core so that the SQL queries sent to the database are logged.

## Creating and Applying the Migration

To create the migration that will set up the database schema, use a PowerShell command prompt to run the command shown in Listing 18-12 in the WebApp project folder.

Listing 18-12. Creating an Entity Framework Core Migration
```ps
dotnet ef migrations add Initial
```

Once the migration has been created, apply it to the database using the command shown in Listing 18-13.

Listing 18-13. Applying the Migration to the Database
```ps
dotnet ef database update
```

The logging messages displayed by the application will show the SQL commands that are sent to the database.

> If you need to reset the database, then run the `dotnet ef database drop --force` command and then the command in Listing 18-13.

## Adding the CSS Framework

Later chapters will demonstrate the different ways that HTML responses can be generated. Run the commands shown in Listing 18-14 to remove any existing version of the LibMan package and install the version used in this book. (You can skip these commands if you installed this version of LibMan in earlier chapters.)

Listing 18-14. Installing the LibMan Tool Package
```ps
dotnet tool uninstall --global Microsoft.Web.LibraryManager.Cli
dotnet tool install --global Microsoft.Web.LibraryManager.Cli --version 2.1.113
```

To add the Bootstrap CSS framework so that the HTML responses can be styled, run the commands shown in Listing 18-15 in the WebApp project folder.

Listing 18-15. Installing the Bootstrap CSS Framework
```ps
libman init -p cdnjs
libman install bootstrap@5.1.3 -d wwwroot/lib/bootstrap
```

## Configuring the Request Pipeline

To define a simple middleware component that will be used to make sure the example project has been set up correctly, add a class file called *TestMiddleware.cs* to the *WebApp* folder and add the code shown in Listing 18-16.

Listing 18-16. The Contents of the TestMiddleware.cs File in the WebApp Folder
```cs
using WebApp.Models;

namespace WebApp 
{
	public class TestMiddleware 
	{
		private RequestDelegate nextDelegate;
		
		public TestMiddleware(RequestDelegate next) 
		{
			nextDelegate = next;
		}
		
		public async Task Invoke(HttpContext context, DataContext dataContext) 
		{
			if (context.Request.Path == "/test") 
			{
				await context.Response.WriteAsync($"There are {dataContext.Products.Count()} products\n");
				await context.Response.WriteAsync($"There are {dataContext.Categories.Count()} categories\n");
				await context.Response.WriteAsync($"There are {dataContext.Suppliers.Count()} suppliers\n");
			}
			else
			{
				await nextDelegate(context);
			}
		}
	}
}
```

Add the middleware component to the request pipeline, as shown in Listing 18-17.

Listing 18-17. Adding a Middleware Component in the Program.cs File in the WebApp Folder
```cs
using Microsoft.EntityFrameworkCore;
using WebApp.Models;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<DataContext>(opts => 
{
	opts.UseSqlServer(builder.Configuration["ConnectionStrings:ProductConnection"]);
	opts.EnableSensitiveDataLogging(true);
});

var app = builder.Build();

app.UseMiddleware<WebApp.TestMiddleware>();
app.MapGet("/", () => "Hello World!");

var context = app.Services.CreateScope().ServiceProvider.GetRequiredService<DataContext>();
SeedData.SeedDatabase(context);

app.Run();
```

## Running the Example Application

Start the application by running the command shown in Listing 18-18 in the WebApp project folder.

Listing 18-18. Running the Example Application
```ps
dotnet run
```

Use a new browser tab and request *http://localhost:5000/test*, and you will see the response shown in Figure 18-2.
