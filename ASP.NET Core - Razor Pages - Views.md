#aspnet_core/razor_pages/view

---

The view part of a Razor Page uses the same syntax and has the same features as the views used with controllers. Razor Pages can use the full range of expressions and features such as sessions, temp data, and layouts. Aside from the use of the `@page` directive and the *page model classes*, the only differences are a certain amount of duplication to configure features such as layouts and partial views, as described in the sections that follow.

# Creating a Layout for Razor Pages

Layouts for Razor Pages are created in the same way as for controller views but in the *Pages/Shared* folder. If you are using Visual Studio, create the *Pages/Shared* folder and add to it a file named *\_Layout.cshtml* using the Razor Layout template with the contents shown in Listing 23-19. If you are using Visual Studio Code, create the Pages/Shared folder, create the \_Layout.cshtml file in the new folder, and add the content shown in Listing 23-19.

> Layouts can be created in the same folder as the Razor Pages that use them, in which case they will be used in preference to the files in the Shared folder.

Listing 23-19. The Contents of the _Layout.cshtml File in the Pages/Shared Folder
```html
<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
	<title>@ViewBag.Title</title>
</head>
<body>
	<h5 class="bg-secondary text-white text-center m-2 p-2">Razor Page</h5>
	@RenderBody()
</body>
</html>
```

The layout doesn’t use any features that are specific to Razor Pages and contains the same elements and expressions used in Chapter 22 when I created a layout for the controller views.
Next, use the Razor View Start template to add a file named \_ViewStart.cshtml to the Pages folder. Visual Studio will create the file with the content shown in Listing 23-20. If you are using Visual Studio Code, create the \_ViewStart.cshtml file and add the content shown in Listing 23-20.

Listing 23-20. The Contents of the \_ViewStart.cshtml File in the Pages Folder
```html
@{
Layout = "_Layout";
}
```

The C# classes generated from Razor Pages are derived from the `Page` class, which provides the `Layout` property used by the view start file, which has the same purpose as the one used by controller views. In Listing 23-21, I have updated the `Index` page to remove the elements that will be provided by the layout.

Listing 23-21. Removing Elements in the Index.cshtml File in the Pages Folder
```html
@page "{id:long?}"
@model IndexModel
<div class="bg-primary text-white text-center m-2 p-2">@Model.Product?.Name</div>
```

Using a view start file applies the layout to all pages that don’t override the value assigned to the `Layout` property. In Listing 23-22, I have added a code block to the *Editor* page so that it doesn’t use a layout. 

Listing 23-22. Disabling Layouts in the Editor.cshtml File in the Pages Folder
```html
@page "{id:long}"
@model EditorModel
@{
	Layout = null;
}
<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<! ...elements omitted for brevity ... />
</body>
</html>
```

# Using Partial Views in Razor Pages

Razor Pages can use partial views so that common content isn’t duplicated. The example in this section relies on the *tag helpers* feature, which I describe in detail in Chapter 25. For this chapter, add the directive shown in Listing 23-23 to the view imports file, which enables the custom HTML element used to apply partial views.

Listing 23-23. Enabling Tag Helpers in the _ViewImports.cshtml File in the Pages Folder
```html
@namespace WebApp.Pages
@using WebApp.Models
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```


Next, add a Razor view named \_ProductPartial.cshtml in the *Pages/Shared* folder and add the content shown in Listing 23-24.
Listing 23-24. The Contents of the _ProductPartial.cshtml File in the Pages/Shared Folder
```html
@model Product
<div class="m-2">
	<table class="table table-sm table-striped table-bordered">
		<tbody>
			<tr><th>Name</th><td>@Model?.Name</td></tr>
			<tr><th>Price</th><td>@Model?.Price</td></tr>
		</tbody>
	</table>
</div>
```

Notice there is nothing specific to Razor Pages in the partial view. Partial views use the `@model` directive to receive a view model object and do not use the `@page` directive or have page models, both of which are specific to Razor Pages. This allows Razor Pages to share partial views with MVC controllers, as described in the sidebar.

![[zICO - Warning - 16.png]] **UNDERSTANDING THE PARTIAL METHOD SEARCH PATH**

The Razor view engine starts looking for a partial view in the same folder as the Razor Page that uses it. If there is no matching file, then the search continues in each parent directory until the Pages folder is reached. For a partial view used by a Razor Page defined in the *Pages/App/Data* folder, for example, the view engine looks in the *Pages/App/Data* folder, the *Page/App* folder, and then the *Pages* folder. If no file is found, the search continues to the *Pages/Shared* folder and, finally, to the *Views/Shared* folder. The last search location allows partial views defined for use with controllers to be used by Razor Pages, which is a useful feature for avoiding duplicate content in applications where MVC controllers and Razor Pages are both used.

Partial views are applied using the `partial` element, as shown in Listing 23-25, with the name attribute specifying the name of the view and the model attribute providing the view model.

> ![[[zICO - Warning - 16.png]] Partial views receive a view model through their `@model` directive and not a page model. It is for this reason that the value of the model attribute is `Model.Product` and not just `Model`.

Listing 23-25. Using a Partial View in the Index.cshtml File in the Pages Folder
```html
@page "{id:long?}"
@model IndexModel
<div class="bg-primary text-white text-center m-2 p-2">@Model.Product?.Name</div>
<partial name="_ProductPartial" model="Model.Product" />
```

When the Razor Page is used to handle a response, the contents of the partial view are incorporated into the response.

# Creating Razor Pages Without Page Models

If a Razor Page is simply presenting data to the user, the result can be a page model class that simply declares a constructor dependency to set a property that is consumed in the view. To understand this pattern, add a Razor Page named *Data.cshtml* to the *WebApp/Pages* folder with the content shown in Listing 23-26.

Listing 23-26. The Contents of the Data.cshtml File in the Pages Folder
```html
@page
@model DataPageModel
@using Microsoft.AspNetCore.Mvc.RazorPages

<h5 class="bg-primary text-white text-center m-2 p-2">Categories</h5>
<ul class="list-group m-2">
	@foreach (Category c in Model.Categories) 
	{
		<li class="list-group-item">@c.Name</li>
	}
</ul>

@functions 
{
	public class DataPageModel : PageModel 
	{
		private DataContext context;
		
		public IEnumerable<Category> Categories { get; set; } = Enumerable.Empty<Category>();
		
		public DataPageModel(DataContext ctx) 
		{
			context = ctx;
		}
		
		public void OnGet() 
		{
			Categories = context.Categories;
		}
	}
}
```

The page model in this example doesn’t transform data, perform calculations, or do anything other than giving the view access to the data through dependency injection. To avoid this pattern, where a page model class is used only to access a service, the `@inject` directive can be used to obtain the service in the view, without the need for a page model, as shown in Listing 23-27.

> ![[zICO - Warning - 16.png]] The `@inject` directive should be used sparingly and only when the page model class adds no value other than to provide access to services. In all other situations, using a page model class is easier to manage and maintain.

Listing 23-27. Accessing a Service in the Data.cshtml File in the Pages Folder
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
```

The `@inject` expression specifies the service type and the name by which the service is accessed. In this example, the service type is `DataContext`, and the name by which it is accessed is context. Within the view, the `@foreach` expression generates elements for each object returned by the `DataContext.Categories` properties. Since there is no page model in this example, I have removed the `@page` and `@using` directives.