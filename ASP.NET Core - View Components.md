#aspnet_core/view_components

---

# Preparing for This Chapter

This chapter uses the WebApp project from Chapter 23. To prepare for this chapter, add a class file named City.cs to the WebApp/Models folder with the content shown in Listing 24-1.

Listing 24-1. The Contents of the City.cs File in the Models Folder
```cs
namespace WebApp.Models 
{
	public class City 
	{
		public string? Name { get; set; }
		public string? Country { get; set; }
		public int? Population { get; set; }
	}
}
```

Add a class named *CitiesData.cs* to the *WebApp/Models* folder with the content shown in Listing 24-2.

Listing 24-2. The Contents of the CitiesData.cs File in the WebApp/Models Folder
```cs
namespace WebApp.Models 
{
	public class CitiesData 
	{
		private List<City> cities = new List<City> 
		{
			new City { Name = "London", Country = "UK", Population = 8539000},
			new City { Name = "New York", Country = "USA", Population = 8406000 },
			new City { Name = "San Jose", Country = "USA", Population = 998537 },
			new City { Name = "Paris", Country = "France", Population = 2244000 }
		};
		
		public IEnumerable<City> Cities => cities;
		
		public void AddCity(City newCity) 
		{
			cities.Add(newCity);
		}
	}
}
```

The `CitiesData` class provides access to a collection of `City` objects and provides an `AddCity` method that adds a new object to the collection. Add the statement shown in Listing 24-3 to the Program.cs file to create a service for the `CitiesData` class.

Listing 24-3. Defining a Service in the Program.cs File in the WebApp Folder
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

builder.Services.AddSingleton<CitiesData>();

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

The new statement uses the `AddSingleton` method to create a `CitiesData` service. There is no interface/implementation separation in this service, which I have created to easily distribute a shared `CitiesData` object. Add a Razor Page named *Cities.cshtml* to the *WebApp/Pages* folder and add the content shown in Listing 24-4.

Listing 24-4. The Contents of the Cities.cshtml File in the Pages Folder
```html
@page
@inject CitiesData Data
<div class="m-2">
	<table class="table table-sm table-striped table-bordered">
		<tbody>
			@foreach (City c in Data.Cities) 
			{
				<tr>
					<td>@c.Name</td>
					<td>@c.Country</td>
					<td>@c.Population</td>
				</tr>
			}
		</tbody>
	</table>
</div>
```

## Dropping the Database

Open a new PowerShell command prompt, navigate to the folder that contains the WebApp.csproj file, and run the command shown in Listing 24-5 to drop the database.

Listing 24-5. Dropping the Database
`dotnet ef database drop --force`

## Running the Example Application

Use the PowerShell command prompt to run the command shown in Listing 24-6.

Listing 24-6. Running the Example Application
`dotnet run`

# Understanding View Components

Applications commonly need to embed content in views that isn’t related to the main purpose of the application. Common examples include site navigation tools and authentication panels that let the user log in without visiting a separate page. The data for this type of feature isn’t part of the model data passed from the action method or page model to the view. It is for this reason that I have created two sources of data in the example project: I am going to display some content generated using `City` data, which isn’t easily done in a view that receives data
from the Entity Framework Core repository and the `Product`, `Category`, and `Supplier` objects it contains.

**Partial views** are used to create reusable markup that is required in views, avoiding the need to duplicate the same content in multiple places in the application. Partial views are a useful feature, but they just contain fragments of HTML and Razor directives, and the data they operate on is received from the parent view. If you need to display different data, then you run into a problem. You could access the data you need directly from the partial view, but this breaks the development model and produces an application that is difficult to understand and maintain. Alternatively, you could extend the view models used by the application so that it includes the data you require, but this means you have to change every action method, which makes it hard to isolate the functionality of action methods for effective maintenance and testing.
This is where **view components** come in. A view component is a C# class that provides a partial view with the data that it needs, independently from the action method or Razor Page. In this regard, a view component can be thought of as a specialized action or page, but one that is used only to provide a partial view with data; it cannot receive HTTP requests, and the content that it provides will always be included in the parent view.

# Creating and Using a View Component

A view component is any class whose name ends with *ViewComponent* and that defines an `Invoke` or `InvokeAsync` method or any class that is derived from the `ViewComponent` base class or that has been decorated with the `ViewComponent` attribute. I demonstrate the use of the attribute in the “Getting Context Data” section, but the other examples in this chapter rely on the base class.

View components can be defined anywhere in a project, but the convention is to group them in a folder named *Components*. Create the *WebApp/Components* folder and add to it a class file named CitySummary.cs with the content shown in Listing 24-7.

Listing 24-7. The Contents of the CitySummary.cs File in the Components Folder
```cs
using Microsoft.AspNetCore.Mvc;
using WebApp.Models;

namespace WebApp.Components 
{
	public class CitySummary : ViewComponent 
	{
		private CitiesData data;
		
		public CitySummary(CitiesData cdata) 
		{
			data = cdata;
		}
		
		public string Invoke() 
		{
			return $"{data.Cities.Count()} cities, " + $"{data.Cities.Sum(c => c.Population)} people";
		}
	}
}
```

View components can take advantage of dependency injection to receive the services they require. In this example, the view component declares a dependency on the `CitiesData` class, which is then used in the `Invoke` method to create a string that contains the number of cities and the population total.

# Applying a View Component Using a Component Property

View components can be applied in two different ways. 

The first technique is to use the `Component` property that is added to the C# classes generated from views and Razor Pages. This property returns an object that implements the `IViewComponentHelper` interface, which provides the `InvokeAsync` method. Listing 24-8
uses this technique to apply the view component in the Index.cshtml file in the Views/Home folder.

Listing 24-8. Using a View Component in the Index.cshtml File in the Views/Home Folder
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

<tr><th>Name</th><td>@Model?.Name</td></tr>
<tr><th>Price</th><td>@Model?.Price.ToString("c")</td></tr>
<tr><th>Category ID</th><td>@Model?.CategoryId</td></tr>

@section Footer 
{
	@(((Model?.Price / ViewBag.AveragePrice)* 100).ToString("F2"))% of average price
}

@section Summary 
{
	<div class="bg-info text-white m-2 p-2">
	@await Component.InvokeAsync("CitySummary")
	</div>
}
```

View components are applied using the `Component.InvokeAsync` method, using the name of the view component class as the argument. The syntax for this technique can be confusing. View component classes define either an `Invoke` or `InvokeAsync` method, depending on whether their work is performed synchronously or asynchronously. But the `Component.InvokeAsync` method is always used, even to apply view components that define the `Invoke` method and whose operations are entirely synchronous.

To add the namespace for the view components to the list that are included in views, I added the statement shown in Listing 24-9 to the \_ViewImports.json file in the Views folder.
Listing 24-9. Adding a Namespace in the \_ViewImports.json File in the Views Folder
```html
@using WebApp.Models
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using WebApp.Components
```

# Applying View Components Using a Tag Helper

Razor Views and Pages can contain tag helpers, which are custom HTML elements that are managed by C# classes. I explain how tag helpers work in detail in Chapter 25, but view components can be applied using an HTML element that is implemented as a tag helper. To enable this feature, add the directive shown in Listing 24-10 to the \_ViewImports.cshtml file in the Views folder.

![[zICO - Warning - 16.png]] View components can be used only in controller views or Razor Pages and cannot be used to handle requests directly.

Listing 24-10. Configuring a Tag Helper in the _ViewImports.cshtml File in the Views Folder
```html
@using WebApp.Models
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using WebApp.Components
@addTagHelper *, WebApp
```

The new directive adds tag helper support for the example project, which is specified by name, and which is WebApp for this example. In Listing 24-11, I have used the custom HTML element to apply the view component.

Listing 24-11. Applying a View Component in the Index.cshtml File in the Views/Home Folder
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

<tr><th>Name</th><td>@Model?.Name</td></tr>
<tr><th>Price</th><td>@Model?.Price.ToString("c")</td></tr>
<tr><th>Category ID</th><td>@Model?.CategoryId</td></tr>

@section Footer 
{
	@(((Model?.Price / ViewBag.AveragePrice)* 100).ToString("F2"))% of average price
}

@section Summary 
{
	<div class="bg-info text-white m-2 p-2">
		<vc:city-summary />
	</div>
}
```

The tag for the custom element is `vc`, followed by a colon, followed by the name of the view component class, which is transformed into **kebab-case**. Each capitalized word in the class name is converted to lowercase and separated by a hyphen so that *CitySummary* becomes *city-summary*, and the `CitySummary` view component is applied using the `vc:city-summary` element.

# Applying View Components in Razor Pages

Razor Pages use view components in the same way, either through the `Component` property or through the custom HTML element. Since Razor Pages have their own view imports file, a separate `@addTagHelper` directive is required, as shown in Listing 24-12.

Listing 24-12. Adding a Directive in the \_ViewImports.cshtml File in the Pages Folder
```html
@namespace WebApp.Pages
@using WebApp.Models
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper *, WebApp
@page
@inject DataContext context;

<h5 class="bg-primary text-white text-center m-2 p-2">Categories</h5>
<ul class="list-group m-2">
	@foreach (Category c in context.Categories) 
	{
		<li class="list-group-item">@c.Name</li>
	}
</ul>
<div class="bg-info text-white m-2 p-2">
	<vc:city-summary />
</div>
```

# Understanding View Component Results

The ability to insert simple string values into a view or page isn’t especially useful, but fortunately, view components are capable of much more. More complex effects can be achieved by having the Invoke or `InvokeAsync` method return an object that implements the `IViewComponentResult` interface. There are three built-in classes that implement the `IViewComponentResult` interface, and they are described in Table 24-3, along with the convenience methods for creating them provided by the `ViewComponent` base class. I describe
the use of each result type in the sections that follow.

Table 24-3. The Built-in IViewComponentResult Implementation Classes

Name|Description
--|--
ViewViewComponentResult|This class is used to specify a Razor View, with optional view model data. Instances of this class are created using the `View` method.
ContentViewComponentResult|This class is used to specify a text result that will be safely encoded for inclusion in an HTML document. Instances of this class are created using the `Content` method.
HtmlContentViewComponentResult|This class is used to specify a fragment of HTML that will be included in the HTML document without further encoding. There is no `ViewComponent` method to create this type of result.

**There is special handling for two result types**. If a view component returns a string, then it is used to create a `ContentViewComponentResult` object, which is what I relied on in earlier examples. If a view component returns an `IHtmlContent` object, then it is used to create an
`HtmlContentViewComponentResult` object.

# Returning a Partial View

The most useful response is the awkwardly named `ViewViewComponentResult` object, which tells Razor to render a partial view and include the result in the parent view. The `ViewComponent` base class provides the `View` method for creating `ViewViewComponentResult` objects, and four versions of the method are available, described in Table 24-4.

Table 24-4. The ViewComponent.View Methods

Name|Description
--|--
View()|Using this method selects the default view for the view component and does not provide a view model.
View(model)|Using the method selects the default view and uses the specified object as the view model.
View(viewName)|Using this method selects the specified view and does not provide a view model.
View(viewName, model)|Using this method selects the specified view and uses the specified object as the view model.

These methods correspond to those provided by the `Controller` base class and are used in much the same way. To create a view model class that the view component can use, add a class file named *CityViewModel.cs* to the *WebApp/Models* folder and use it to define the class shown in Listing 24-14.

Listing 24-14. The Contents of the CityViewModel.cs File in the Models Folder
```cs
namespace WebApp.Models 
{
	public class CityViewModel 
	{
		public int? Cities { get; set; }
		public int? Population { get; set; }
	}
}
```

Listing 24-15 modifies the Invoke method of the `CitySummary` view component so it uses the `View` method to select a partial view and provides view data using a `CityViewModel` object.

Listing 24-15. Selecting a View in the CitySummary.cs File in the Components Folder
```cs
using Microsoft.AspNetCore.Mvc;
using WebApp.Models;

namespace WebApp.Components 
{
	public class CitySummary : ViewComponent 
	{
		private CitiesData data;
		
		public CitySummary(CitiesData cdata) 
		{
			data = cdata;
		}
		
		public IViewComponentResult Invoke() 
		{
			return View(new CityViewModel 
			{
				Cities = data.Cities.Count(),
				Population = data.Cities.Sum(c => c.Population)
			});
		}
	}
}
```

There is no view available for the view component currently, but the error message this produces reveals the locations that are searched. Restart ASP.NET Core and use a browser to request http://localhost:5000/home/index/1 to see the locations that are searched when the view component is used with a controller. Request http://localhost:5000/data to see the locations searched when a view component is used with a Razor Page. Figure 24-4 shows both responses.

Figure 24-4. The search locations for view component views
![[zIMG-asp.net-view-components-24-4.png]]

Razor searches for a view named *Default.cshtml* when a view component invokes the `View` method without specifying a name. If the view component is used with a controller, then the search locations are as follows:
```txt
/Views/[controller]/Components/[viewcomponent]/Default.cshtml
/Views/Shared/Components/[viewcomponent]/Default.cshtml
/Pages/Shared/Components/[viewcomponent]/Default.cshtml
```

When the `CitySummary` component is rendered by a view selected through the `Home` controller, for example, `[controller]` is `Home` and `[viewcomponent]` is `CitySummary`, which means the first search location is */Views/Home/Components/CitySummary/Default.cshtml*. If the view component is used with a Razor Page, then the search locations are as follows:
```txt
/Pages/Components/[viewcomponent]/Default.cshtml
/Pages/Shared/Components/[viewcomponent]/Default.cshtml
/Views/Shared/Components/[viewcomponent]/Default.cshtml
```

If the search paths for Razor Pages do not include the page name but a Razor Page is defined in a subfolder, then the Razor view engine will look for a view in the `Components/[viewcomponent]` folder, relative to the location in which the Razor Page is defined, working its way up the folder hierarchy until it finds a view or reaches the Pages folder.

> Notice that view components used in Razor Pages will find views defined in the *Views/Shared/Components* folder and that view components defined in controllers will find views in the *Pages/Shared/Components* folder. This means you don’t have to duplicate views when a view component is used by controllers and Razor Pages.

Create the *WebApp/Views/Shared/Components/CitySummary* folder and add to it a Razor View named *Default.cshtml* with the content shown in Listing 24-16.

Listing 24-16. The Default.cshtml File in the Views/Shared/Components/CitySummary Folder
```html
@model CityViewModel
<table class="table table-sm table-bordered text-white bg-secondary">
	<thead>
		<tr><th colspan="2">Cities Summary</th></tr>
	</thead>
	<tbody>
		<tr>
			<td>Cities:</td>
			<td class="text-right">@Model?.Cities</td>
		</tr>
		<tr>
			<td>Population:</td>
			<td class="text-right">@Model?.Population?.ToString("#,###")</td>
		</tr>
	</tbody>
</table>
```

Views for view components are similar to partial views and use the `@model` directive to set the type of the view model object. This view receives a `CityViewModel` object from its view component, which is used to populate the cells in an HTML table. Restart ASP.NET Core and a browser to request http://localhost:5000/home/index/1 and http://localhost:5000/data, and you will see the view incorporated into the responses, as shown in Figure 24-5.

Figure 24-5. Using a view with a view component
![[zIMG-asp.net-view-components-24-5.png]]

# Returning HTML Fragments

The `ContentViewComponentResult` class is used to include fragments of HTML in the parent view without using a view. Instances of the `ContentViewComponentResult` class are created using the `Content` method inherited from the `ViewComponent` base class, which accepts a string value. Listing 24-17 demonstrates the use of the `Content` method.

> In addition to the `Content` method, the `Invoke` method can return a string, which will be automatically converted to a `ContentViewComponentResult`. This is the approach I took in the view component when it was first defined.

Listing 24-17. Using the Content Method in the CitySummary.cs File in the Components Folder
```cs
using Microsoft.AspNetCore.Mvc;
using WebApp.Models;

namespace WebApp.Components 
{
	public class CitySummary : ViewComponent 
	{
		private CitiesData data;
		
		public CitySummary(CitiesData cdata) 
		{
			data = cdata;
		}
		
		public IViewComponentResult Invoke() 
		{
			return Content("This is a <h3><i>string</i></h3>");
		}
	}
}
```

**The string received by the `Content` method is encoded to make it safe to include in an HTML document**. This is particularly important when dealing with content that has been provided by users or external systems because it prevents JavaScript content from being embedded into the HTML generated by the application. In this example, the string that I passed to the `Content` method contains some basic HTML tags.

Restart ASP.NET Core and use a browser to request http://localhost:5000/home/index/1. The response will include the encoded HTML fragment, as shown in Figure 24-6.
Figure 24-6. Returning an encoded HTML fragment using a view component
![[zIMG-asp.net-view-components-24-6.png]]

If you look at the HTML that the view component produced, you will see that the angle brackets have been replaced so that the browser doesn’t interpret the content as HTML elements, as follows:
```html
<div class="bg-info text-white m-2 p-2">
This is a &lt;h3&gt;&lt;i&gt;string&lt;/i&gt;&lt;/h3&gt;
</div>
```

You don’t need to encode content if you trust its source and want it to be interpreted as HTML. The `Content` method always encodes its argument, so you must create the `HtmlContentViewComponentResult` object directly and provide its constructor with an `HtmlString` object, which represents a string that you know is safe to display, either because it comes from a source that you trust or because you are confident that it has already been encoded, as shown in Listing 24-18.

Listing 24-18. Returning an HTML Fragment in the CitySummary.cs File in the Components Folder
```cs
using Microsoft.AspNetCore.Mvc;
using WebApp.Models;
using Microsoft.AspNetCore.Mvc.ViewComponents;
using Microsoft.AspNetCore.Html;

namespace WebApp.Components 
{
	public class CitySummary : ViewComponent 
	{
		private CitiesData data;
		
		public CitySummary(CitiesData cdata) 
		{
			data = cdata;
		}
		
		public IViewComponentResult Invoke() 
		{
			return new HtmlContentViewComponentResult(new HtmlString("This is a <h3><i>string</i></h3>"));
		}
	}
}
```

This technique should be used with caution and only with sources of content that cannot be tampered with and that perform their own encoding. Restart ASP.NET Core and use a browser to request http://localhost:5000/home/index/1, and you will see the response isn’t encoded and is interpreted as HTML elements, as shown in Figure 24-7.

Figure 24-7. Returning an unencoded HTML fragment using a view component
![[zIMG-asp.net-view-components-24-7.png]]
