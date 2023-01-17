#aspnet_core/view_components

---

# Getting Context Data

Details about the current request and the parent view are provided to a view component through properties defined by the `ViewComponent` base class, as described in Table 24-5.

Table 24-5. The ViewComponentContext Properties ^596308

Name|Description
--|--
HttpContext|This property returns an `HttpContext` object that describes the current request and the response that is being prepared.
Request|This property returns an `HttpRequest` object that describes the current HTTP request.
User|This property returns an `IPrincipal` object that describes the current user, as described in Chapters 37 and 38.
RouteData|This property returns a `RouteData` object that describes the routing data for the current request.
ViewBag|This property returns the dynamic view bag object, which can be used to pass data between the view component and the view, as described in Chapter 22.
ModelState|This property returns a `ModelStateDictionary`, which provides details of the model binding process, as described in Chapter 29.
ViewData|This property returns a `ViewDataDictionary`, which provides access to the view data provided for the view component.

The context data can be used in whatever way helps the view component do its work, including varying the way that data is selected or rendering different content or views. It is hard to devise a representative example of using context data in a view component because the problems it solves are specific to each project.

In Listing 24-19, I check the route data for the request to determine whether the routing pattern contains a controller segment variable, which indicates a request that will be handled by a controller and view.

Listing 24-19. Using Request Data in the CitySummary.cs File in the Components Folder
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
		
		public string Invoke() 
		{
			if (RouteData.Values["controller"] != null) 
			{
				return "Controller Request";
			} 
			else
			{
				return "Razor Page Request";
			}
		}
	}
}
```

# Providing Context from the Parent View Using Arguments

Parent views can provide additional context data to view components, providing them with either data or guidance about the content that should be produced. The context data is received through the `Invoke` or `InvokeAsync` method, as shown in Listing 24-20.

Listing 24-20. Receiving a Value in the CitySummary.cs File in the Components Folder
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
		
		public IViewComponentResult Invoke(string themeName) 
		{
			ViewBag.Theme = themeName;
			return View(
				new CityViewModel 
				{
					Cities = data.Cities.Count(),
					Population = data.Cities.Sum(c => c.Population)
				});
		}
	}
}
```

The `Invoke` method defines a `themeName` parameter that is passed on to the partial view using the view bag, which was described in Chapter 22. Listing 24-21 updates the *Default* view to use the received value to style the content it produces.

Listing 24-21. Styling Content in the Default.cshtml File in the Views/Shared/Components/CitySummary Folder
```html
@model CityViewModel
<table class="table table-sm table-bordered text-white bg-@ViewBag.Theme">
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

**A value for all parameters defined by a view component’s `Invoke` or `InvokeAsync` method must always be provided**. Listing 24-22 provides a value for `themeName` parameter in the view selected by the `Home` controller.

> ![[zICO - Exclamation - 16.png]] The view component will not be used if you do not provide values for all the parameters it defines, but no error message is displayed. If you don’t see any content from a view component, then the likely cause is a missing parameter value.

Listing 24-22. Supplying a Value in the Index.cshtml File in the Views/Home Folder
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
<tr>
	<th>Category ID</th>
	<td>@Model?.CategoryId</td>
</tr>

@section Footer 
{
	@(((Model?.Price / ViewBag.AveragePrice)* 100).ToString("F2"))% of average price
}

@section Summary 
{
	<div class="bg-info text-white m-2 p-2">
		<vc:city-summary theme-name=”secondary” />
	</div>
}
```

The name of each parameter **is expressed an attribute using kebab-case** so that the `theme-name` attribute provides a value for the `themeName` parameter. Listing 24-23 sets a value in the Data.cshtml Razor Page.

Listing 24-23. Supplying a Value in the Data.cshtml File in the Pages Folder
```html
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
	<vc:city-summary theme-name="danger" />
</div>
```

Figure 24-9. Using context data in a view component
![[zIMG-asp.net-view-components-24-9.png]]

## PROVIDING VALUES USING THE COMPONENT HELPER

If you prefer applying view components using the `Component.InvokeAsync` helper, then you can provide context using method arguments, like this:
```html
<div class="bg-info text-white m-2 e-2">
	@await Component.InvokeAsync("CitySummary", new { themeName"= "daneer" })
</div>
```

The first argument to the `InvokeAsync` method is the name of the view component class. The second argument is an object whose names correspond to the parameters defined by the view component.

## Using a Default Parameter Value

Default values can be defined for the `Invoke` method parameters, as shown in Listing 24-24, which provides a fallback if the parent view doesn’t provide a value.

Listing 24-24. Defining a Default Value in the CitySummary.cs File in the Components Folder
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
		
		public IViewComponentResult Invoke(string themeName = "success") 
		{
			ViewBag.Theme = themeName;
			return View(
				new CityViewModel 
				{
					Cities = data.Cities.Count(),
					Population = data.Cities.Sum(c => c.Population)
				});
		}
	}
}
```

The default value is *success*, and it will be used if the view component is applied without a `theme-name` attribute, as shown in Listing 24-25.

Listing 24-25. Omitting the Attribute in the Data.cshtml File in the Pages Folder
```html
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

# Creating Asynchronous View Components

All the examples so far in this chapter have been *synchronous view components*, which can be recognized because they define the `Invoke` method. If your view component relies on asynchronous APIs, then you can create an *asynchronous view component* by defining an `InvokeAsync` method that returns a `Task`. When Razor receives the `Task` from the `InvokeAsync` method, it will wait for it to complete and then insert the result into the main view. To create a new component, add a class file named PageSize.cs to the *Components* folder and use it to define the class shown in Listing 24-26.

Listing 24-26. The Contents of the PageSize.cs File in the Components Folder
```cs
using Microsoft.AspNetCore.Mvc;

namespace WebApp.Components 
{
	public class PageSize : ViewComponent 
	{
		public async Task<IViewComponentResult> InvokeAsync() 
		{
			HttpClient client = new HttpClient();
			HttpResponseMessage response = await client.GetAsync("http://apress.com");
			return View(response.Content.Headers.ContentLength);
		}
	}
}
```

The `InvokeAsync` method uses the `async` and `await` keywords to consume the asynchronous API provided by the `HttpClient` class and get the length of the content returned by sending a `GET` request to *Apress.com*. The length is passed to the `View` method, which selects the default partial view associated with the view component.
Create the *Views/Shared/Components/PageSize* folder and add to it a Razor View named *Default.cshtml* with the content shown in Listing 24-27.

Listing 24-27. The Contents of the Default.cshtml File in the Views/Shared/Components/PageSize Folder
```html
@model long
<div class="m-1 p-1 bg-light text-dark">Page size: @Model</div>
```

The final step is to use the component, which I have done in the `Index` view used by the `Home` controller, as shown in Listing 24-28. No change is required in the way that asynchronous view components are used.

Listing 24-28. Using an Asynchronous Component in the Index.cshtml File in the Views/Home Folder
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
<tr>
	<th>Category ID</th>
	<td>@Model?.CategoryId</td>
</tr>

@section Footer 
{
	@(((Model?.Price / ViewBag.AveragePrice)* 100).ToString("F2"))% of average price
}

@section Summary 
{
	<div class="bg-info text-white m-2 p-2">
		<vc:city-summary theme-name="secondary" />
		<vc:page-size />
	</div>
}
```
