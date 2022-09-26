#EF_Core 

---

Entity Framework Core must be configured so that it knows the type of database to which it will connect,
which connection string describes that connection, and which context class will present the data in the
database. Listing 7-17 shows the required changes to the Program.cs file.

```cs
using Microsoft.EntityFrameworkCore;
using SportsStore.Models;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllersWithViews();
// DB cintext initializtion
builder.Services.AddDbContext<StoreDbContext>(opts => 
{
	opts.UseSqlServer(builder.Configuration["ConnectionStrings:SportsStoreConnection"]);
});

var app = builder.Build();

app.UseStaticFiles();
app.MapDefaultControllerRoute();

app.Run();
```

The IConfiguration interface provides access to the ASP.NET Core configuration system, which
includes the contents of the appsettings.json file and which I describe in detail in Chapter 15 #TODO/AddLink. 
Access to the configuration data is through the builder.Configuration property, which allows the database
connection string to be obtained. Entity Framework Core is configured with the AddDbContext method,
which registers the database context class and configures the relationship with the database. The
UseSQLServer method declares that SQL Server is being used.