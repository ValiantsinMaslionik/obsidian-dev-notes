#aspnet_core/mvc/view

---

## Prepare sample apllication

This chapter uses the WebApp project from Chapter 20 [[ASP.NET Core - Web Services - Advanced Features]] . To prepare for this chapter, replace the contents of the Program.cs file with the statements shown in Listing 21-1, which removes some of the services and middleware used in earlier chapters.

Listing 21-1. Replacing the Contents of the Program.cs File in the WebApp Folder
```cs
using Microsoft.EntityFrameworkCore;
using WebApp.Models;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<DataContext>(opts => 
{
	opts.UseSqlServer(builder.Configuration["ConnectionStrings:ProductConnection"]);
	opts.EnableSensitiveDataLogging(true);
});

builder.Services.AddControllers();

var app = builder.Build();
app.UseStaticFiles();
app.MapControllers();

var context = app.Services.CreateScope().ServiceProvider.GetRequiredService<DataContext>();
SeedData.SeedDatabase(context);

app.Run();
```

### Dropping the Database

Open a new PowerShell command prompt, navigate to the folder that contains the WebApp.csproj file, and run the command shown in Listing 21-2 to drop the database.

Listing 21-2. Dropping the Database
`dotnet ef database drop --force`

### Running the Example Application

Use the PowerShell command prompt to run the command shown in Listing 21-3. Listing 21-3. Running the Example Application
`dotnet watch`

The database will be seeded as part of the application startup. Once ASP.NET Core is running, use a web browser to request *http://localhost:5000/api/products*, which will produce the response shown in Figure 21-1.

Figure 21-1. Running the example application
![[zIMG-asp.net-mvc-views-21-1.png]]

This chapter uses `dotnet watch`, rather than the `dotnet run` command used in earlier chapters. The `dotnet watch` command is useful when working with views because changes are pushed to the browser automatically. At some point, however, you will make a change that cannot be processed by the `dotnet watch` command, and you will see a message like this at the command prompt:
```txt
watch : Unable to apply hot reload because of a rude edit.
watch : Do you want to restart your app - Yes (y) / No (n) / Always (a) / Never (v)?
```

The point at which this arises depends on the editor you have chosen, but when this happens, select the `Always` option so that the application will always be restarted when a reload cannot be performed.

## Getting Started with Views

I started this chapter with a web service controller to demonstrate the similarity with a controller that uses views. It is easy to think about web service and view controllers as being separate, but it is important to understand that the same underlying features are used for both types of response. In the sections that follow, I configure the application to support HTML applications and repurpose the `Home` controller so that it produces an HTML response.

### Configuring the Application

The first step is to configure ASP.NET Core to enable HTML responses, as shown in Listing 21-4.

Listing 21-4. Changing the Configuration in the Program.cs File in the WebApp Folder
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

var app = builder.Build();
app.UseStaticFiles();
app.MapControllers();
app.MapControllerRoute("Default", "{controller=Home}/{action=Index}/{id?}");

var context = app.Services.CreateScope().ServiceProvider.GetRequiredService<DataContext>();
SeedData.SeedDatabase(context);

app.Run();
```

HTML responses are created using *views*, which are files containing a mix of HTML elements and C# expressions. The `AddControllers` method I used in Chapter 19 to enable the MVC Framework only supports web service controllers. To enable support for *views*, the `AddControllersWithViews` method is used.
The second change is the addition of the `MapControllerRoute` method in the endpoint routing configuration. Controllers that generate HTML responses don’t use the same routing attributes that are applied to web service controllers and rely on a feature named *convention routing*, which I describe in the next section.

### Creating an HTML Controller

Controllers for HTML applications are similar to those used for web services but with **some important differences**. To create an HTML controller, add a class file named HomeController.cs to the *Controllers* folder with the statements shown in Listing 21-5.

Listing 21-5. The Contents of the HomeController.cs File in the Controllers Folder
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
	}
}
```

The base class for HTML controllers is `Controller`, which is derived from the `ControllerBase` class used for web service controllers and provides additional methods that are specific to working with views.
*Action methods* in HTML controllers return objects that implement the `IActionResult` interface, which is the same result type used in Chapter 19 to return specific status code responses. The Controller base class provides the `View` method, which is used to select a view that will be used to create a response.

> ![[zICO - Warning - 16.png]] Notice that the controller in Listing 21-5 hasn’t been decorated with attributes. The `ApiController` attribute is applied only to web service controllers and should not be used for HTML controllers. 
> ![[zICO - Exclamation - 16.png]] The Route and HTTP method attributes are not required because HTML controllers rely on convention-based routing, which was configured in Listing 21-4 and which is introduced shortly.

The `View` method creates an instance of the `ViewResult` class, which implements the `IActionResult` interface and tells the MVC Framework that a view should be used to produce the response for the client.
The argument to the `View` method is called the *view model* and provides the view with the data it needs to generate a response. There are no views for the MVC Framework to use at the moment, but if you restart ASP.NET Core and use a browser to request *http://localhost:5000*, you will see an error message that shows how the MVC Framework responds to the ViewResult it received from the Index action method, as shown in Figure 21-2.

Figure 21-2. Using a view result
![[zIMG-asp.net-mvc-views-21-2.png]] ^da053f

Behind the scenes, there are two important conventions at work, which are described in the following sections.

> Two features can expand the range of search locations. The search will include the */Pages/Shared* folder if the project uses Razor Pages, as explained in Chapter 23.

### Understanding Convention Routing

HTML controllers rely on *convention routing* instead of the `Route` attribute. The convention in this term refers to the use of the controller class name and the action method name used to configure the routing system, which was done in Listing 21-5 by adding this statement to the endpoint routing configuration:

The route that this statement sets up matches two- and three-segment URLs. The value of the first segment is used as the name of the controller class, without the `Controller` suffix, so that `Home` refers to the `HomeController` class. The second segment is the name of the action method, and the optional third segment allows action methods to receive a parameter named id. Default values are used to select the `Index` action method on the `Home` controller for URLs that do not contain all the segments. This is such a common convention that the same routing configuration can be set up without having to specify the URL pattern, as shown in Listing 21-6.

Listing 21-6. Using the Default Routing Convention in the Program.cs File in the WebApp Folder
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

var app = builder.Build();
app.UseStaticFiles();
app.MapControllers();
app.MapDefaultControllerRoute();

var context = app.Services.CreateScope().ServiceProvider.GetRequiredService<DataContext>();
SeedData.SeedDatabase(context);

app.Run();
```

The `MapDefaultControllerRoute` method avoids the risk of mistyping the URL pattern and sets up the convention-based routing. I have configured one route in this chapter, but an application can define as many routes as it needs, and later chapters expand the routing configuration to make examples easier to follow.

> The MVC Framework assumes that any public method defined by an HTML controller is an action method and that action methods support all HTTP methods. If you need to define a method in a controller that is not an action, you can make it private or, if that is not possible, decorate the method with the `NonAction` attribute. 
 
You can restrict an action method to support specific HTTP methods by applying attributes so that the `HttpGet` attribute denotes an action that handles `GET` requests, the `HttpPost` method denotes an action that handles `POST` requests, and so on.

### Understanding the Razor View Convention

When the `Index` action method defined by the `Home` controller is invoked, it uses the value of the `id` parameter to retrieve an object from the database and passes it to the `View` method.
```cs
public async Task<IActionResult> Index(long id = 1) 
{
	return View(await context.Products.FindAsync(id));
}
```

When an action method invokes the `View` method, it creates a `ViewResult` that tells the MVC Framework to use the default convention to locate a view. 
The Razor view engine looks for a view **with the same name as the action method**, with the addition of the *cshtml* file extension, which is the file type used by the Razor view engine. Views are stored in the *Views* folder, **grouped by the controller** they are associated with. 
- The first location searched is the *Views/Home* folder, since the action method is defined by the *Home* controller (the name of which is taken by dropping *Controller* from the name of the controller class). 
- If the *Index.cshtml* file cannot be found in the *Views/Home* folder, then the *Views/Shared* folder is checked, which is the location where views that are shared between controllers are stored.

> While most controllers have their own views, views can also be shared so that common functionality doesn’t have to be duplicated, as demonstrated in the “Using Shared Views” section.

The exception response in [[#^da053f|Figure 21-2]] shows the result of both conventions. The routing conventions are used to process the request using the `Index` action method defined by the `Home` controller, which tells the Razor view engine to use the view search convention to locate a view. The view engine uses the name of the action method and controller to build its search pattern and checks for the *Views/Home/Index.cshtml* and
*Views/Shared/Index.cshtml* files.

## Selecting a View by Name

The action method in Listing 21-5 relies entirely on convention, leaving Razor to select the view that is used to generate the response. Action methods can select a view by providing a name as an argument to the `View` method, as shown in Listing 21-9.

Listing 21-9. Selecting a View in the HomeController.cs File in the Controllers Folder
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
			Product? prod = await context.Products.FindAsync(id);
			if (prod?.CategoryId == 1) 
			{
				return View("Watersports", prod);
			}
			else 
			{
				return View(prod);
			}
		}
	}
}
```

The action method selects the view based on the `CategoryId` property of the `Product` object that is retrieved from the database. If the `CategoryId` is `1`, the action method invokes the `View` method with an additional argument that selects a view named *Watersports*.
```cs
return View("Watersports", prod);
```

Notice that the action method doesn’t specify the file extension or the location for the view. It is the job of the view engine to translate *Watersports* into a view file.
If you save the *HomeController.cs* file, `dotnet watch` will detect the change and reload the browser, which will cause an error because the view file doesn’t exist. To create the view, add a Razor View file named *Watersports.cshtml* to the Views/Home folder with the content shown in Listing 21-10.

Listing 21-10. The Contents of the Watersports.cshtml File in the Views/Home Folder
```html
<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<h6 class="bg-secondary text-white text-center m-2 p-2">Watersports</h6>
	<div class="m-2">
		<table class="table table-sm table-striped table-bordered">
			<tbody>
				<tr><th>Name</th><td>@Model?.Name</td></tr>
				<tr><th>Price</th><td>@Model?.Price.ToString("c")</td></tr>
				<tr><th>Category ID</th><td>@Model?.CategoryId</td></tr>
			</tbody>
		</table>
	</div>
</body>
</html>
```

The new view follows the same pattern as the `Index` view but has a different title above the table. Save the change and request *http://localhost:5000/home/index/1* and *http://localhost:5000/home/index/4*. The action method selects the *Watersports* view for the first URL and the default view for the second URL, producing the two responses shown in Figure 21-6.

Figure 21-6. Selecting views
![[zIMG-asp.net-mvc-views-21-6.png]]

## Using Shared Views

When the Razor view engine locates a view, it looks in the `View/[controller]` folder and then the `Views/Shared` folder. This search pattern means that views that contain common content can be shared between controllers, avoiding duplication. To see how this process works, add a Razor View file named *Common.cshtml* to the *Views/Shared* folder with the content shown in Listing 21-11.

Listing 21-11. The Contents of the Common.cshtml File in the Views/Shared Folder
```html
<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<h6 class="bg-secondary text-white text-center m-2 p-2">Shared View</h6>
</body>
</html>
```

Next, add an action method to the `Home` controller that uses the new view, as shown in Listing 21-12. 

Listing 21-12. Adding an Action in the HomeController.cs File in the Controllers Folder
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
			Product? prod = await context.Products.FindAsync(id);
			if (prod?.CategoryId == 1) 
			{
				return View("Watersports", prod);
			}
			else 
			{
				return View(prod);
			}
		}
		
		public IActionResult Common() 
		{
			return View();
		}
	}
}
```

The new action *relies on the convention of using the method name as the name of the view*. When a view doesn’t require any data to display to the user, the `View` method can be called without arguments. 

Next, create a new controller by adding a class file named *SecondController.cs* to the *Controllers* folder, with the code shown in Listing 21-13.

Listing 21-13. The Contents of the SecondController.cs File in the Controllers Folder
```cs
using Microsoft.AspNetCore.Mvc;

namespace WebApp.Controllers 
{
	public class SecondController : Controller 
	{
		public IActionResult Index() 
		{
			return View("Common");
		}
	}
}
```

The new controller defines a single action, named `Index`, which invokes the `View` method to select the `Common` view. Wait for the application to be built and navigate to http://localhost:5000/home/common and http://localhost:5000/second, both of which will render the `Common` view.

## Specifying a view locations

The Razor view engine will look for a controller-specific view before a shared view. You can change this behavior by specifying the complete path to a view file, which can be useful if you want to select a shared view that would otherwise be ignored because there is a controller-specific view with the same name.
```cs
public IActionResult Index() 
{
	return View("/Views/Shared/Common.cshtml");
}
```

When specifying the view, **the path relative to the project folder** must be specified, starting with the `/` character. Notice that the **full name of the file, including the file extension, is used.**
==This is a technique that should be used sparingly because it creates a dependency on a specific file, rather than allowing the view engine to select the file. ==