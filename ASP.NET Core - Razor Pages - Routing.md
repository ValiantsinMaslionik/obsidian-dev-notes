#aspnet_core/razor_pages 

---

# Understanding the URL Routing Convention

URL routing for Razor Pages is based on the file name and location, relative to the *Pages* folder. The Razor Page in Listing 23-4 is in a file named *Index.cshtml*, in the *Pages* folder, which means that it will handle requests for the */index*. The routing convention can be  overridden, as described in the [[#Understanding Razor Pages Routing]] section, but, by default, it is the location of the Razor Page file that determines the URLs that it responds to.

# Understanding Razor Pages Routing

Razor Pages rely on the location of the CSHTML file for routing so that a request for http://localhost:5000/index is handled by the *Pages/Index.cshtml* file. Adding a more complex URL structure for an application is done by adding folders whose names represent the segments in the URL you want to support. As an example, create the **_WebApp/Pages/Suppliers_** folder and add to it a Razor Page named *List.cshtml* with the contents shown in Listing 23-5.

Listing 23-5. The Contents of the List.cshtml File in the Pages/Suppliers Folder
```html
@page
@model ListModel
@using Microsoft.AspNetCore.Mvc.RazorPages
@using WebApp.Models;

<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<h5 class="bg-primary text-white text-center m-2 p-2">Suppliers</h5>
	<ul class="list-group m-2">
		@foreach (string s in Model.Suppliers) 
		{
			<li class="list-group-item">@s</li>
		}
	</ul>
</body>
</html>

@functions 
{
	public class ListModel : PageModel 
	{
		private DataContext context;
		public IEnumerable<string> Suppliers { get; set; } = Enumerable.Empty<string>();
		public ListModel(DataContext ctx) 
		{
			context = ctx;
		}
		public void OnGet() 
		{
			Suppliers = context.Suppliers.Select(s => s.Name);
		}
	}
}
```

The new page model class defines a `Suppliers` property that is set to the sequence of `Name` values for the `Supplier` objects in the database. The database operation in this example is synchronous, so the page model class defined the `OnGet` method, rather than `OnGetAsync`. The supplier names are displayed in a list using an `@foreach` expression. To use the new Razor Page, restart ASP.NET Core and use a browser to request http://localhost:5000/suppliers/list, which produces the response shown in Figure 23-4. The path segments of the request URL correspond to the folder and file name of the List.cshtml Razor Page.

Figure 23-4. Using a folder structure to route requests
![[zIMG-asp.net-mvc-views-23-4.png]]

## ![[zICO - Warning - 16.png]] UNDERSTANDING THE DEFAULT URL HANDLING

The `MapRazorPages` method sets up a route for the default URL for the *Index.cshtml* Razor Page, following a similar convention used by the MVC Framework. It is for this reason that the first Razor Page added to a project is usually called *Index.cshtml*. However, when the application mixes Razor Pages and the MVC Framework together, the default route defined by Razor Pages takes precedence because it was created with a lower order (route ordering is described in Chapter 13). This means a request http://localhost:5000 is handled by the *Index.cshtml* Razor Page in the example project and not the `Index` action on the `Home` controller.

If you want the MVC framework to handle the default URL, then you can change the order assigned to the Razor Pages routes, like this:
```cs
app.MapRazorPages().Add(b => ((RouteEndpointBuilder)b).Order = 2);
```

The Razor Pages routes are created with an `Order` of `0`, which gives them precedence over the MVC routes, which are created with an `Order` of `1`. Assigning an `Order` of `2` gives the MVC framework routes precedence.
In my own projects, where I mix Razor Pages and MVC controllers, I tend to rely on the MVC Framework to handle the default URL, and I avoid creating the *Index.cshtml* Razor Page to avoid confusion.

# Specifying a Routing Pattern in a Razor Page

**Using the folder and file structure to perform routing means there are no segment variables for the model binding process to use**. Instead, values for the request handler methods are obtained from the URL query string, which you can see by using a browser to request http://localhost:5000/index?id=2, which produces the response shown in Figure 23-5.

Figure 23-5. Using a query string parameter
![[zIMG-asp.net-mvc-views-23-5.png]]

The query string provides a parameter named `id`, which the model binding process uses to satisfy the `id` parameter defined by the `OnGetAsync` method in the *Index* Razor Page.
```cs
public async Task OnGetAsync(long id = 1) {
```

I explain how model binding works in detail in Chapter 28, but for now, it is enough to know that the query string parameter in the request URL is used to provide the `id` argument when the `OnGetAsync` method is invoked, which is used to query the database for a product.
The `@page` directive can be used with a routing pattern, which allows segment variables to be defined, as shown in Listing 23-6.

Listing 23-6. Defining a Segment Variable in the Index.cshtml File in the Pages Folder
```html
@page "{id:long?}"
@model IndexModel

@using Microsoft.AspNetCore.Mvc.RazorPages
@using WebApp.Models;

<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<div class="bg-primary text-white text-center m-2 p-2">@Model.Product?.Name</div>
</body>
</html>

@functions 
{
// ...statements omitted for brevity...
}
```

All the URL pattern features that are described in [[ASP.NET Core - Routing - URL Patterns]] can be used with the `@page` directive. The route pattern used in Listing 23-6 adds an optional segment variable named `id`, which is constrained so that it will match only those segments that can be parsed to a long value. 

**The `@page` directive can also be used to override the file-based routing convention for a Razor Page**, as shown in Listing 23-7.

Listing 23-7. Changing the Route in the List.cshtml File in the Pages/Suppliers Folder
```html
@page "/lists/suppliers"
@model ListModel

@using Microsoft.AspNetCore.Mvc.RazorPages
@using WebApp.Models;

<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<h5 class="bg-primary text-white text-center m-2 p-2">Suppliers</h5>
	<ul class="list-group m-2">
		@foreach (string s in Model.Suppliers) 
		{
			<li class="list-group-item">@s</li>
		}
	</ul>
</body>
</html>

@functions 
{
	// ...statements omitted for brevity...
}
```

The directive changes the route for the *List* page so that it matches URLs whose path is */lists/suppliers*.

# Adding Routes for a Razor Page

Using the `@page` directive replaces the default file-based route for a Razor Page. If you want to define multiple routes for a page, then configuration statements can be added to the *Program.cs* file, as shown in Listing 23-8.

Listing 23-8. Adding Razor Page Routes in the Program.cs File in the WebApp Folder
```cs
using Microsoft.EntityFrameworkCore;
using WebApp.Models;
using Microsoft.AspNetCore.Mvc.RazorPages;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<DataContext>(opts => 
{
	opts.UseSqlServer(builder.Configuration["ConnectionStrings:ProductConnection"]);
	opts.EnableSensitiveDataLogging(true);
});

builder.Services.AddControllersWithViews();
builder.Services.AddRazorPages();
builder.Services.AddDistributedMemoryCache();
builder.Services.AddSession(options => 
{
	options.Cookie.IsEssential = true;
});

builder.Services.Configure<RazorPagesOptions>(opts => 
{
	opts.Conventions.AddPageRoute("/Index", "/extra/page/{id:long?}");
});

var app = builder.Build();

app.UseStaticFiles();
app.UseSession();
app.MapControllers();
app.MapDefaultControllerRoute();
app.MapRazorPages();

var context = app.Services.CreateScope().ServiceProvider.GetRequiredService<DataContext>();
SeedData.SeedDatabase(context);

app.Run();
```

The *options pattern* is used to add additional routes for a Razor Page using the `RazorPageOptions` class. The `AddPageRoute` extension method is called on the `Conventions` property to add a route for a page. 
- The first argument is the path to the page, without the file extension and relative to the `Pages` folder. 
- The second argument is the URL pattern to add to the routing configuration. 

The route added in Listing 23-8 supplements the route defined by the `@page` attribute, which you can test by requesting http://localhost:5000/index/2, which will produce the response shown on the right of Figure 23-7.

Figure 23-7. Adding a route for a Razor Page
![[zIMG-asp.net-mvc-views-23-7.png]]
