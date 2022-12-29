
#ASP_NET_CORE/RESTfull 

---

As you learn about the facilities that ASP.NET Core provides for web services, it can be easy to forget they arebuilt on the features described in Part 2. To create a simple web service, add the statements shown in Listing 19-3 to the Program.cs file.

Listing 19-3. Creating a Web Service in the Program.cs File in the Platform Folder
```cs
using Microsoft.EntityFrameworkCore;
using WebApp.Models;
using System.Text.Json;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<DataContext>(opts => 
{
	opts.UseSqlServer(builder.Configuration["ConnectionStrings:ProductConnection"]);
	opts.EnableSensitiveDataLogging(true);
});

const string BASEURL = "api/products";

var app = builder.Build();

app.MapGet($"{BASEURL}/{{id}}", async (HttpContext context, DataContext data) => 
{
	string? id = context.Request.RouteValues["id"] as string;
	if (id != null) 
	{
		Product? p = data.Products.Find(long.Parse(id));
		if (p == null) 
		{
			context.Response.StatusCode = StatusCodes.Status404NotFound;
		}
		else 
		{
			context.Response.ContentType = "application/json";
			await context.Response.WriteAsync(JsonSerializer.Serialize<Product>(p));
		}
	}
});

app.MapGet(BASEURL, async (HttpContext context, DataContext data) => 
{
	context.Response.ContentType = "application/json";
	await context.Response.WriteAsync(JsonSerializer.Serialize<IEnumerable<Product>>(data.Products));
});

app.MapPost(BASEURL, async (HttpContext context, DataContext data) => 
{
	Product? p = await JsonSerializer.DeserializeAsync<Product>(context.Request.Body);
	if (p != null) 
	{
		await data.AddAsync(p);
		await data.SaveChangesAsync();
		context.Response.StatusCode = StatusCodes.Status200OK;
	}
});

app.MapGet("/", () => "Hello World!");

var context = app.Services.CreateScope().ServiceProvider.GetRequiredService<DataContext>();
SeedData.SeedDatabase(context);

app.Run();
```

The same API that I used to register endpoints in earlier chapters can be used to create a web service, using only features that you have seen before. The `MapGet` and `MapPost` methods are used to create three routes, all of which match URLs that start with */api*, ==which is the conventional prefix for web services==.

The endpoint for the first route receives a value from a segment variable that is used to locate a single `Product` object in the database. The endpoint for the second route retrieves all the `Product` objects in the database. The third endpoint handles `POST` requests and reads the request body to get a JSON representation of a new object to add to the database.

There are better ASP.NET Core features for creating web services, which you will see shortly, but the code in Listing 19-3 shows how the HTTP method and the URL can be combined to describe an operation, which is the key concept in creating web services.

> The responses shown in the figure contain null values for the Supplier and Category properties because the LINQ queries do not include related data. See Chapter 20 for details.

Testing the third route requires a different approach because it isn’t possible to send HTTP `POST` requests using the browser. Open a new PowerShell command prompt and run the command shown in Listing 19-4. 
It is important to enter the command exactly as shown because the `Invoke-RestMethod` command is fussy about the syntax of its arguments.

> You may receive an error when you use the `Invoke-RestMethod` or `Invoke-WebRequest` command to test the examples in this chapter if you have not performed the initial setup for Microsoft Edge. The problem can be fixed by running the browser and selecting the initial configurations you require.

Listing 19-4. Sending a POST Request
```ps
Invoke-RestMethod http://localhost:5000/api/products -Method POST -Body (@{Name="Swimming Goggles"; Price=12.75; CategoryId=1; SupplierId=1} | ConvertTo-Json) -ContentType "application/json"
```
