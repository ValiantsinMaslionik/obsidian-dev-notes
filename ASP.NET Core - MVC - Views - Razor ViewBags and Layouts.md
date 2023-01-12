#aspnet_core/razor 

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

# Working with Layouts

The views in the example application contain duplicate elements that deal with setting up the HTML document, defining the head section, loading the Bootstrap CSS file, and so on. Razor supports *layouts*, which consolidate common content in a single file that can be used by any view.
Layouts are typically stored in the *Views/Shared* folder because they are usually used by the action methods of more than one controller. If you are using Visual Studio, right-click the *Views/Shared* folder, select Add ➤ New Item from the pop-up menu, and choose the Razor Layout template, as shown in Figure 22-4. Make sure the name of the file is *\_Layout.cshtml* and click the *Add* button to create the new file.

Replace the content added to the file by Visual Studio with the elements shown in Listing 22-9.

If you are using Visual Studio Code, create a file named *\_Layout.cshtml* in the *Views/Shared* folder and add the content shown in Listing 22-9.

Listing 22-9. The Contents of the *\_Layout.cshtml* File in the *Views/Shared* Folder
```html
<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<h6 class="bg-primary text-white text-center m-2 p-2">Shared View</h6>
	@RenderBody()
</body>
</html>
```

The layout contains the common content that will be used by multiple views. The content that is unique to each view is inserted into the response by calling the `RenderBody` method, which is inherited by the `RazorPage<T>` class, as described in Chapter 21. Views that use layouts can focus on just their unique content, as shown in Listing 22-10.

Listing 22-10. Using a Layout in the *Index.cshtml* File in the Views/Home Folder
```html
@model Product
@{
	Layout = "_Layout";
}

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
```

The layout is selected by adding a code block, denoted by the `@{` and `}` characters, that sets the `Layout` property inherited from the `RazorPage<T>` class. In this case, the `Layout` property is set to the name of the layout file. As with normal views, the layout is specified without a path or file extension, and the Razor engine will search in the 
- `/Views/[controller]` 
- `/Views/Shared` 
folders to find a matching file. 

# Configuring Layouts Using the View Bag

The view can provide the layout with data values, allowing the common content provided by the view to be customized. The view bag properties are defined in the code block that selects the layout, as shown in Listing 22-11.

Listing 22-11. Setting a View Bag Property in the Index.cshtml File in the Views/Home Folder
```html
@model Product
@{
	Layout = "_Layout";
	ViewBag.Title = "Product Table";
}
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
```

The view sets a `Title` property, which can be used in the layout, as shown in Listing 22-12.

Listing 22-12. Using a View Bag Property in the \_Layout.cshtml File in the Views/Shared Folder
```html
<!DOCTYPE html>
<html>
<head>
	<title>@ViewBag.Title</title>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<h6 class="bg-primary text-white text-center m-2 p-2">@(ViewBag.Title ?? "Layout")</h6>
	@RenderBody()
</body>
</html>
```

The `Title` property is used to set the content of the title element and `h6` element in the body section. Layouts cannot rely on view bag properties being defined, which is why the expression in the `h6` element provides a fallback value if the view doesn’t define a `Title` property. 

## ![[zICO - Warning - 16.png]] UNDERSTANDING VIEW BAG PRECEDENCE

**The values defined by the view take precedence if the same view bag property is defined by the view and the action method**. If you want to allow the action to override the value defined in the view, then use a statement like this in the view code block:
```html
@{
	Layout = "_Layout";
	ViewBag.Title = ViewBag.Title ?? "Product Table";
}
```

This statement will set the value for the `Title` property only if it has not already been defined by the action method.

# Using a View Start File

Instead of setting the `Layout` property in every view, you can add a view start file to the project that provides a default `Layout` value. If you are using Visual Studio, right-click the *Views* folder item in the Solution Explorer, select Add ➤ New Item, and locate the *Razor View Start* template. Make sure the name of the file is \_ViewStart.cshtml and click the *Add* button to create the file, which will have the content shown in Listing 22-13.

If you are using Visual Studio Code, then add a file named \_ViewStart.cshtml to the *Views* folder and add the content shown in Listing 22-13.
Listing 22-13. The Contents of the \_ViewStart.cshtml File in the *Views* Folder
```html
@{
	Layout = "_Layout";
}
```

The file contains sets the `Layout` property, and the value will be used as the default. Listing 22-14 removes the content from the Common.cshtml file that is contained in the layout.

Listing 22-14. Removing Content in the Common.cshtml File in the Views/Shared Folder
```html
<h6 class="bg-secondary text-white text-center m-2 p-2">Shared View</h6>
```

The view doesn’t define a view model type and doesn’t need to set the `Layout` property because the project contains a view start file. 

# Overriding the Default Layout

There are two situations where you may need to define a `Layout` property in a view even when there is a view start file in the project. 
In the first situation, a view requires a different layout from the one specified by the view start file. To demonstrate, add a Razor layout file named \_ImportantLayout.cshtml to the *Views/Shared* folder with the content shown in Listing 22-15.

Listing 22-15. The Contents of the \_ImportantLayout.cshtml File in the *Views/Shared* Folder
```html
<!DOCTYPE html>
<html>
<head>
	<title>@ViewBag.Title</title>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<h3 class="bg-warning text-white text-center p-2 m-2">Important</h3>
	@RenderBody()
</body>
</html>
```

In addition to the HTML document structure, this file contains a header element that displays "Important" in large text. Views can select this layout by assigning its name to the `Layout` property, as shown in Listing 22-16.

> If you need to use a different layout for all the actions of a single controller, then add a view start file to the `Views/[controller]` folder that selects the view you require. The Razor engine will use the layout specified by the controller-specific view start file.

Listing 22-16. Using a Specific Layout in the Index.cshtml File in the Views/Home Folder
```html
@model Product
@{
	Layout = "_ImportantLayout";
	ViewBag.Title = "Product Table";
}
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
```

The Layout value in the view start file is overridden by the value in the view, allowing different layouts to be applied. 

## ![[zICO - Warning - 16.png]] SELECTING A LAYOUT PROGRAMMATICALLY

The value that a view assigns to the `Layout` property can be the result of an expression that allows layouts to be selected by the view, similar to the way that action methods can select views. Here is an example that selects the layout based on a property defined by the view model object:
```html
@model Product
@{
	Layout = Model.Price > 100 ? "_ImportantLayout" : "_Layout";
	ViewBag.Title = ViewBag.Title ?? "Product Table";
}
```

The layout named \_ImportantLayout is selected when the value of the view model object’s `Price` property is greater than 100; otherwise, \_Layout is used.

The second situation where a Layout property can be needed is when a view contains a complete HTML document and doesn’t require a layout at all. To see the problem, open a new PowerShell command prompt and run the command shown in Listing 22-17.

Listing 22-17. Sending an HTTP Request
`Invoke-WebRequest http://localhost:5000/home/list | Select-Object -expand Content`

This command sends an HTTP GET request whose response will be produced using the L*ist.cshtml* file in the *Views/Home* folder. This view contains a complete HTML document, which is combined with the content in the view specified by the view start file, producing a malformed HTML document, like this:
```html
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<h6 class="bg-primary text-white text-center m-2 p-2">Layout</h6>
	<!DOCTYPE html>
	<html>
	<head>
		<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
	</head>
	<body>
		<h6 class="bg-secondary text-white text-center m-2 p-2">Products</h6>
		<div class="m-2">
			<table class="table table-sm table-striped table-bordered">
				<thead><tr><th>Name</th><th>Price</th></tr></thead>
				<tbody>
					<!-- ...table rows omitted for brevity... -->
				</tbody>
			</table>
		</div>
	</body>
	</html>
</body>
</html>
```

The structural elements for the HTML document are duplicated, so there are two `html`, `head`, `body`, and `link` elements. Browsers are adept at handling malformed HTML but don’t always cope with poorly structured content. 
Where a view contains a complete HTML document, the `Layout` property can be set to null, as shown in Listing 22-18.

Listing 22-18. Disabling Layouts in the List.cshtml File in the Views/Home Folder
```html
@model IEnumerable<Product>
@{
	Layout = null;
	decimal average = Model?.Average(p => p.Price) ?? 0;
}
<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<h6 class="bg-secondary text-white text-center m-2 p-2">Products</h6>
	<div class="m-2">
		<table class="table table-sm table-striped table-bordered">
			<thead>
				<tr><th>Name</th><th>Price</th></tr>
			</thead>
			<tbody>
				@foreach (Product p in Model ?? Enumerable.Empty<Product>()) 
				{
					<tr>
						<td>@p.Name</td><td>@p.Price</td>
						<td>@((p.Price / average * 100).ToString("F1"))% of average</td>
					</tr>
				}
			</tbody>
		</table>
	</div>
</body>
</html>
```

Save the view and run the command shown in Listing 22-17 again, and you will see that the response contains only the elements in the view and that the layout has been disabled.
```html
<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<h6 class="bg-secondary text-white text-center m-2 p-2">Products</h6>
	<div class="m-2">
		<table class="table table-sm table-striped table-bordered">
			<thead><tr><th>Name</th><th>Price</th></tr></thead>
			<tbody>
			<!-- ...table rows omitted for brevity... -->
			</tbody>
		</table>
	</div>
</body>
</html>
```

# Using Layout Sections

The Razor View engine supports the concept of sections, which allow you to provide regions of content within a layout.  **Razor sections give greater control over which parts of the view are inserted into the layout and where they are placed**. 
To demonstrate the sections feature, I have edited the */Views/Home/Index.cshtml* file, as shown in Listing 22-19. The browser will display an error when you save the changes in this listing, which will be resolved when you make corresponding changes in the next listing.

Listing 22-19. Defining Sections in the Index.cshtml File in the Views/Home Folder
```html
@model Product
@{
	Layout = "_Layout";
	ViewBag.Title = ViewBag.Title ?? "Product Table";
}

@section Header 
{
	Product Information
}

<tr>
	<th>Name</th>
	<td>@Model?.Name</td>
</tr>
<tr>
	<th>Price</th>
	<td>@Model?.Price.ToString("c")</td>
</tr>
<tr><th>Category ID</th><td>@Model?.CategoryId</td></tr>

@section Footer 
{
	@(((Model?.Price / ViewBag.AveragePrice)* 100).ToString("F2"))% of average price
}
```

Sections are defined using the Razor `@section` expression followed by a name for the section. Listing 22-19 defines sections named `Header` and `Footer`, and sections can contain the same mix of HTML content and expressions, just like the main part of the view. Sections are applied in a layout with the `@RenderSection` expression, as shown in Listing 22-20.

Listing 22-20. Using Sections in the \_Layout.cshtml File in the Views/Shared Folder
```html
<!DOCTYPE html>
<html>
<head>
	<title>@ViewBag.Title</title>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<div class="bg-info text-white m-2 p-1">This is part of the layout</div>
	<h6 class="bg-primary text-white text-center m-2 p-2">@RenderSection("Header")</h6>
	<div class="bg-info text-white m-2 p-1">This is part of the layout</div>
	<div class="m-2">
		<table class="table table-sm table-striped table-bordered">
			<tbody>
				@RenderBody()
			</tbody>
		</table>
	</div>
	<div class="bg-info text-white m-2 p-1">This is part of the layout</div>
	<h6 class="bg-primary text-white text-center m-2 p-2">@RenderSection("Footer")</h6>
	<div class="bg-info text-white m-2 p-1">This is part of the layout</div>
</body>
</html>
```

When the layout is applied, the `RenderSection` expression inserts the content of the specified section into the response. **The regions of the view that are not contained within a section are inserted into the response by the `RenderBody` method**. 

> ![[zICO - Warning - 16.png]] A view can define only the sections that are referred to in the layout. The view engine throws an exception if you define sections in the view for which there is no corresponding `@RenderSection` expression in the layout.

Sections allow views to provide fragments of content to the layout without specifying how they are used. As an example, Listing 22-21 redefines the layout to consolidate the body and sections into a single HTML table.

Listing 22-21. Using a Table in the \_Layout.cshtml File in the Views/Shared Folder
```html
<!DOCTYPE html>
<html>
<head>
	<title>@ViewBag.Title</title>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<div class="m-2">
		<table class="table table-sm table-striped table-bordered">
			<thead>
				<tr>
					<th class="bg-primary text-white text-center" colspan="2">@RenderSection("Header")</th>
				</tr>
			</thead>
			<tbody>
				@RenderBody()
			</tbody>
			<tfoot>
				<tr>
					<th class="bg-primary text-white text-center" colspan="2">@RenderSection("Footer")</th>
				</tr>
			</tfoot>
		</table>
	</div>
</body>
</html>
```

## Using Optional Layout Sections

By default, a view must contain all the sections for which there are `RenderSection` calls in the layout, and an exception will be thrown if the layout requires a section that the view hasn’t defined. Listing 22-22 adds a call to the `RenderSection` method that requires a section named `Summary`.

Listing 22-22. Adding a Section in the \_Layout.cshtml File in the Views/Shared Folder
```html
<!DOCTYPE html>
<html>
<head>
	<title>@ViewBag.Title</title>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<div class="m-2">
		<table class="table table-sm table-striped table-bordered">
			<thead>
				<tr>
					<th class="bg-primary text-white text-center" colspan="2">@RenderSection("Header")</th>
				</tr>
			</thead>
			<tbody>
				@RenderBody()
			</tbody>
			<tfoot>
				<tr>
					<th class="bg-primary text-white text-center" colspan="2">@RenderSection("Footer")</th>
				</tr>
			</tfoot>
		</table>
	</div>
	@RenderSection("Summary")
</body>
</html>
```

Figure 22-12. Attempting to render a nonexistent view section
![[zIMG-asp.net-mvc-views-22-12.png]]

There are two ways to solve this problem. 
The first is to create an *optional section*, which will be rendered only if it is defined by the view. Optional sections are created by passing a second argument to the `RenderSection` method, as shown in Listing 22-23.

Listing 22-23. Defining an Optional Section in the \_Layout.cshtml File in the Views/Shared Folder
```html
<!DOCTYPE html>
<html>
<head>
	<title>@ViewBag.Title</title>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<div class="m-2">
		<table class="table table-sm table-striped table-bordered">
			<thead>
				<tr>
					<th class="bg-primary text-white text-center" colspan="2">@RenderSection("Header", false)</th>
				</tr>
			</thead>
			<tbody>
				@RenderBody()
			</tbody>
			<tfoot>
				<tr>
					<th class="bg-primary text-white text-center" colspan="2">@RenderSection("Footer", false)</th>
				</tr>
			</tfoot>
		</table>
	</div>
	@RenderSection("Summary", false)
</body>
</html>
```

The second argument specifies whether a section is required, and using `false` prevents an exception when the view doesn’t define the section.

## Testing for Layout Sections

The `IsSectionDefined` method is used to determine whether a view defines a specified section and can be used in an if expression to render fallback content, as shown in Listing 22-24.

Listing 22-24. Checking for a Section in the \_Layout.cshtml File in the Views/Shared Folder
```html
<!DOCTYPE html>
<html>
<head>
	<title>@ViewBag.Title</title>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<div class="m-2">
		<table class="table table-sm table-striped table-bordered">
			<thead>
				<tr>
					<th class="bg-primary text-white text-center" colspan="2">@RenderSection("Header", false)</th>
				</tr>
			</thead>
			<tbody>
				@RenderBody()
			</tbody>
			<tfoot>
				<tr>
					<th class="bg-primary text-white text-center" colspan="2">@RenderSection("Footer", false)</th>
				</tr>
			</tfoot>
		</table>
	</div>
	@if (IsSectionDefined("Summary")) 
	{
		@RenderSection("Summary", false)
	} 
	else
	{
		<div class="bg-info text-center text-white m-2 p-2">This is the default summary</div>
	}
</body>
</html>
```

The `IsSectionDefined` method is invoked with the name of the section you want to check and returns `true` if the view defines that section. In the example, I used this helper to render fallback content when the view does not define the `Summary` section.