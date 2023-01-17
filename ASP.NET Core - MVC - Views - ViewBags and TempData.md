#aspnet_core/mvc/view

---

# Preparing for This Chapter

This chapter uses the WebApp project from Chapter 21. To prepare for this chapter, replace the contents of the HomeController.cs file with the code shown in Listing 22-1.

Listing 22-1. The Contents of the HomeController.cs File in the Controllers Folder
```cs
using Microsoft.AspNetCore.Mvc;
using WebApp.Models;

namespace WebApp.Controllers 
{
	public class HomeController : Controller 
	{
		private DataContext context;
		
		public HomeController(DataContext ctx) 
		{
			context = ctx;
		}
		
		public async Task<IActionResult> Index(long id = 1) 
		{
			return View(await context.Products.FindAsync(id));
		}
		
		public IActionResult List() 
		{
			return View(context.Products);
		}
	}
}
```

One of the features used in this chapter requires the session feature, which was described in Chapter 16.
To enable sessions, add the statements shown in Listing 22-2 to the Program.cs file.

Listing 22-2. Enabling Sessions in the Program.cs File in the WebApp Folder
```cs
using Microsoft.EntityFrameworkCore;
using WebApp.Models;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<DataContext>(opts => 
{
	opts.UseSqlServer(builder.Configuration["ConnectionStrings:ProductConnection"]);
	opts.EnableSensitiveDataLogging(true);
});

builder.Services.AddControllersWithViews();
builder.Services.AddDistributedMemoryCache();
builder.Services.AddSession(options => 
{
	options.Cookie.IsEssential = true;
});

var app = builder.Build();

app.UseStaticFiles();
app.UseSession();
app.MapControllers();
app.MapDefaultControllerRoute();

var context = app.Services.CreateScope().ServiceProvider.GetRequiredService<DataContext>();
SeedData.SeedDatabase(context);

app.Run();
```

## Dropping the Database

Open a new PowerShell command prompt, navigate to the folder that contains the WebApp.csproj file, and run the command shown in Listing 22-3 to drop the database.

Listing 22-3. Dropping the Database
`dotnet ef database drop --force`

## Running the Example Application
Once the database has been dropped, use the PowerShell command prompt to run the command shown in Listing 22-4.

Listing 22-4. Running the Example Application
`dotnet watch`

The database will be seeded as part of the application startup. Once ASP.NET Core is running, use a web browser to request http://localhost:5000, which will produce the response shown in Figure 22-1.

As noted in Chapter 21, the `dotnet watch` command is useful when working with views, but when you make a change that cannot be handled without restarting ASP.NET Core, you will see a message like this at the command prompt:
```
watch : Unable to apply hot reload because of a rude edit.
watch : Do you want to restart your app - Yes (y) / No (n) / Always (a) / Never (v)?
```

The point at which this arises depends on the editor you have chosen, but when this happens, select the *Alway*s option so that the application will always be restarted when a reload cannot be performed.

# Using the View Bag

Action methods provide views with data to display with a view model, but sometimes additional information is required. Action methods can use the view bag to provide a view with extra data, as shown in Listing 22-5.

Listing 22-5. Using the View Bag in the HomeController.cs File in the Controllers Folder
```cs
using Microsoft.AspNetCore.Mvc;
using WebApp.Models;
using Microsoft.EntityFrameworkCore;

namespace WebApp.Controllers 
{
	public class HomeController : Controller 
	{
		private DataContext context;
		
		public HomeController(DataContext ctx) 
		{
			context = ctx;
		}
		
		public async Task<IActionResult> Index(long id = 1) 
		{
			ViewBag.AveragePrice = await context.Products.AverageAsync(p => p.Price);
			return View(await context.Products.FindAsync(id));
		}
		
		public IActionResult List() 
		{
			return View(context.Products);
		}
	}
}
```

The `ViewBag` property is inherited from the `Controller` base class and returns a *dynamic object*. This allows action methods to create new properties just by assigning values to them, as shown in the listing. The values assigned to the `ViewBag` property by the action method are available to the view through a property also called `ViewBag`, as shown in Listing 22-6.

Listing 22-6. Using the View Bag in the Index.cshtml File in the Views/Home Folder
```html
<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<h6 class="bg-primary text-white text-center m-2 p-2">Product Table</h6>
	<div class="m-2">
		<table class="table table-sm table-striped table-bordered">
			<tbody>
				<tr><th>Name</th><td>@Model?.Name</td></tr>
				<tr><th>Price</th>
				<td>
					@Model?.Price.ToString("c")
					(@(((Model?.Price / ViewBag.AveragePrice)* 100).ToString("F2"))% of average price)
				</td>
				</tr>
				<tr><th>Category ID</th><td>@Model?.CategoryId</td></tr>
			</tbody>
		</table>
	</div>
</body>
</html>
```

The `ViewBag` property conveys the object from the action to the view, alongside the view model object. In the listing, the action method queries for the average of the `Product.Price` properties in the database and assigns it to a view bag property named `AveragePrice`, which the view uses in an expression.

## ![[zICO - Warning - 16.png]] WHEN TO USE THE VIEW BAG

The view bag works best when it is used to provide the view with small amounts of supplementary data without having to create new view model classes for each action method. The problem with the view bag is that the compiler cannot check the use of the properties on dynamic objects, much like views that don’t use an `@model` expression. It can be difficult to judge when a new view model class should be used, and my rule of thumb is to create a new view model class when the same view model property is used by multiple actions or when an action method adds more than two or three properties to the view bag.

# Using Temp Data

The temp data feature allows a controller to preserve data from one request to another, which is useful when performing redirections. **Temp data is stored using a cookie unless session state is enabled when it is stored as session data**. Unlike session data, temp data values are marked for deletion when they are read and removed when the request has been processed.

Add a class file called CubedController.cs to the WebApp/Controllers folder and use it to define the controller shown in Listing 22-7.

Listing 22-7. The Contents of the CubedController.cs File in the Controllers Folder
```cs
using Microsoft.AspNetCore.Mvc;

namespace WebApp.Controllers 
{
	public class CubedController: Controller 
	{
		public IActionResult Index() 
		{
			return View("Cubed");
		}
		
		public IActionResult Cube(double num) 
		{
			TempData["value"] = num.ToString();
			TempData["result"] = Math.Pow(num, 3).ToString();
			return RedirectToAction(nameof(Index));
		}
	}
}
```

The *Cubed* controller defines an `Index` method that selects a view named *Cubed*. There is also a `Cube` action, which relies on the model binding process to obtain a value for its `num` parameter from the request (a process described in detail in Chapter 28). The `Cubed` action method performs its calculation and stores the `num` value and the calculation result using `TempData` property, which returns a dictionary that is used to store key-value pairs. **Since the temp data feature is built on top of the sessions feature, only values that can be serialized to strings can be stored**, which is why I convert both double values to strings in Listing 22-7. Once the values are stored as temp data, the `Cube` method performs a redirection to the `Index` method. To provide the controller with a view, add a Razor View file named *Cubed.cshtml* to the *WebApp/Views/Shared* folder with the content shown in Listing 22-8.

Listing 22-8. The Contents of the Cubed.cshtml File in the Views/Shared Folder
```html
<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<h6 class="bg-secondary text-white text-center m-2 p-2">Cubed</h6>
	<form method="get" action="/cubed/cube" class="m-2">
		<div class="form-group">
			<label>Value</label>
			<input name="num" class="form-control" value="@(TempData["value"])" />
		</div>
		<button class="btn btn-primary mt-1" type="submit">Submit</button>
	</form>
	@if (TempData["result"] != null) 
	{
		<div class="bg-info text-white m-2 p-2">The cube of @TempData["value"] is @TempData["result"]</div>
	}
</body>
</html>
```

The base class used for Razor Views provides access to the temp data through a `TempData` property, allowing values to be read within expressions. In this case, temp data is used to set the content of an input element and display a results summary. Reading a temp data value doesn’t remove it immediately, which means that values can be read repeatedly in the same view. It is only once the request has been processed that the marked values are removed.
To see the effect, use a browser to navigate to http://localhost:5000/cubed, enter a value into the form field, and click the *Submit* button. The browser will send a request that will set the temp data and trigger the redirection. The temp data values are preserved for the new request, and the results are displayed to the user. But reading the data values marks them for deletion, and if you reload the browser, the contents of the input element and the results summary are no longer displayed.

> The object returned by the `TempData` property provides a `Peek` method, which allows you to get a data value without marking it for deletion, and a `Keep` method, which can be used to prevent a previously read value from being deleted. The `Keep` method doesn’t protect a value forever. If the value is read again, it will be marked for removal once more. Use session data if you want to store items so that they won’t be removed when the request is processed.

## ![[zICO - Warning - 16.png]] USING THE TEMP DATA ATTRIBUTE

Controllers can define properties that are decorated with the `TempData` attribute, which is an alternative to using the `TempData` property, like this:
```cs
using Microsoft.AspNetCore.Mvc;

namespace WebApp.Controllers 
{
	public class CubedController: Controller 
	{
		public IActionResult Index() 
		{
			return View("Cubed");
		}
		
		public IActionResult Cube(double num) 
		{
			Value = num.ToString();
			Result = Math.Pow(num, 3).ToString();
			return RedirectToAction(nameof(Index));
		}
		
		[TempData]
		public string? Value { get; set; }
		
		[TempData]
		public string? Result { get; set; }
	}
}
```

The values assigned to these properties are automatically added to the temp data store, and there is no difference in the way they are accessed in the view. My preference is to use the `TempData` dictionary to store values because it makes the intent of the action method obvious to other developers. However, both approaches are entirely valid, and choosing between them is a matter of preference.