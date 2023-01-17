#aspnet_core/view_components

---

View components often provide a summary or snapshot of functionality that is handled in-depth by a controller or Razor Page. For a view component that summarizes a shopping basket, for example, there will often be a link that targets a controller that provides a detailed list of the products in the basket and that can be used to check out and complete the purchase.

In this situation, you can create a class that is a view component as well as a controller or Razor Page. If you are using Visual Studio, expand the *Cities.cshtml* item in the Solution Explorer to show the *Cities.cshtml.cs* file and replace its contents with those shown in Listing 24-29. If you are using Visual Studio Code, add a file named *Cities.cshtml.cs* to the *Pages* folder with the content shown in Listing 24-29.

Listing 24-29. The Contents of the Cities.cshtml.cs File in the Pages Folder
```cs
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.AspNetCore.Mvc.ViewComponents;
using Microsoft.AspNetCore.Mvc.ViewFeatures;
using WebApp.Models;

namespace WebApp.Pages 
{
	[ViewComponent(Name = "CitiesPageHybrid")]
	public class CitiesModel : PageModel 
	{
		public CitiesModel(CitiesData cdata) 
		{
			Data = cdata;
		}
		
		public CitiesData? Data { get; set; }
		
		[ViewComponentContext]
		public ViewComponentContext Context { get; set; } = new();
		
		public IViewComponentResult Invoke() 
		{
			return new ViewViewComponentResult() 
			{
				ViewData = new ViewDataDictionary<CityViewModel>(
					Context.ViewData,
					new CityViewModel 
					{
						Cities = Data?.Cities.Count(),
						Population = Data?.Cities.Sum(c => c.Population)
					})
			};
		}
	}
}
```

This page model class is decorated with the `ViewComponent` attribute, which allows it to be used as a view component. The `Name` argument specifies the name by which the view component will be applied. Since a page model cannot inherit from the `ViewComponent` base class, a property whose type is `ViewComponentContext` is decorated with the `ViewComponentContext` attribute, which signals that it should be assigned an object that defines the properties described in [[ASP.NET Core - View Components - Getting Context Data#^596308|Table 24-5]] before the `Invoke` or `InvokeAsync` method is invoked. The `View` method isn’t available, so I have to create a `ViewViewComponentResult` object, which relies on the context object received through the decorated property. Listing 24-30 updates the view part of the page to use the new page model class.

Listing 24-30. Updating the View in the Cities.cshtml File in the Pages Folder
```html
@page
@model WebApp.Pages.CitiesModel

<div class="m-2">
	<table class="table table-sm table-striped table-bordered">
		<tbody>
			@foreach (City c in Model.Data?.Cities ?? Enumerable.Empty<City>()) 
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

The changes update the directives to use the page model class. To create the view for the hybrid view component, create the *Pages/Shared/Components/CitiesPageHybrid* folder and add to it a Razor View named *Default.cshtml* with the content shown in Listing 24-31.

Listing 24-31. The Default.cshtml File in the Pages/Shared/Components/CitiesPageHybrid Folder
```html
@model CityViewModel
<table class="table table-sm table-bordered text-white bg-dark">
	<thead>
		<tr>
			<th colspan="2">Hybrid Page Summary</th>
		</tr>
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

Listing 24-32 applies the view component part of the hybrid class in another page.

Listing 24-32. Using a View Component in the Data.cshtml File in the Pages Folder
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
	<vc:cities-page-hybrid />
</div>
```

Hybrids are applied just like any other view component. Restart ASP.NET Core and request http://localhost:5000/cities and http://localhost:5000/data. Both URLs are processed by the same class. For the first URL, the class acts as a page model; for the second URL, the class acts as a view component.

Figure 24-12 shows the output for both URLs.
![[zIMG-asp.net-view-components-24-12.png]]

# Creating a Hybrid Controller Class

The same technique can be applied to controllers. Add a class file named *CitiesController.cs* to the *Controllers* folder and add the statements shown in Listing 24-33.

Listing 24-33. The Contents of the CitiesController.cs File in the Controllers Folder
```cs
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.ViewComponents;
using Microsoft.AspNetCore.Mvc.ViewFeatures;
using WebApp.Models;

namespace WebApp.Controllers 
{
	[ViewComponent(Name = "CitiesControllerHybrid")]
	public class CitiesController : Controller 
	{
		private CitiesData data;
		
		public CitiesController(CitiesData cdata) 
		{
			data = cdata;
		}
		
		public IActionResult Index() 
		{
			return View(data.Cities);
		}
		
		public IViewComponentResult Invoke() 
		{
			return new ViewViewComponentResult() 
			{
				ViewData = new ViewDataDictionary<CityViewModel>(
					ViewData,
					new CityViewModel 
					{
						Cities = data.Cities.Count(),
						Population = data.Cities.Sum(c => c.Population)
					});
			};
		}
	}
}
```

A quirk in the way that controllers are instantiated means that a property decorated with the `ViewComponentContext` attribute isn’t required and the `ViewData` property inherited from the `Controller` base class can be used to create the view component result. To provide a view for the action method, create the *Views/Cities* folder and add to it a file named *Index.cshtml* with the content shown in Listing 24-34.

Listing 24-34. The Contents of the Index.cshtml File in the Views/Cities Folder
```html
@model IEnumerable<City>
@{
	Layout = "_ImportantLayout";
}

<div class="m-2">
	<table class="table table-sm table-striped table-bordered">
		<tbody>
			@foreach (City c in Model ?? Enumerable.Empty<City>()) 
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

To provide a view for the view component, create the *Views/Shared/Components/CitiesControllerHybrid* folder and add to it a Razor View named *Default.cshtml* with the content shown in Listing 24-35.

Listing 24-35. The Default.cshtml File in the Views/Shared/Components/CitiesControllerHybrid Folder
```html
@model CityViewModel
<table class="table table-sm table-bordered text-white bg-dark">
	<thead>
		<tr>
			<th colspan="2">Hybrid Controller Summary</th>
		</tr>
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

Listing 24-36 applies the hybrid view component in the Data.cshtml Razor Page, replacing the hybrid class created in the previous section.

Listing 24-36. Applying the View Component in the Data.cshtml File in the Pages Folder
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
	<vc:cities-controller-hybrid />
</div>
```

Restart ASP.NET Core and use a browser to request http://localhost:5000/cities/index and http://localhost:5000/data. For the first URL, the class in Listing 24-36 is used as a controller; for the second URL, the class is used as a view component. Figure 24-13 shows the responses for both URLs.

Figure 24-13. A hybrid controller and view component class
![[zIMG-asp.net-view-components-24-13.png]]