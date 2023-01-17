#aspnet_core/razor_pages 

---

Razor Pages can define handler methods that respond to different HTTP methods. The most common combination is to support the `GET` and `POST` methods that allow users to view and edit data. To demonstrate, add a Razor Page called *Editor.cshtml* to the *Pages* folder and add the content shown in Listing 23-16.

Listing 23-16. The Contents of the Editor.cshtml File in the WebApps/Pages Folder
```html
@page "{id:long}"
@model EditorModel

<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<div class="bg-primary text-white text-center m-2 p-2">Editor</div>
	<div class="m-2">
		<table class="table table-sm table-striped table-bordered">
			<tbody>
				<tr><th>Name</th><td>@Model.Product?.Name</td></tr>
				<tr><th>Price</th><td>@Model.Product?.Price</td></tr>
			</tbody>
		</table>
		<form method="post">
			@Html.AntiForgeryToken()
			<div class="form-group">
				<label>Price</label>
				<input name="price" class="form-control" value="@Model.Product?.Price" />
			</div>
			<button class="btn btn-primary mt-2" type="submit">Submit</button>
		</form>
	</div>
</body>
</html>
```

The elements in the Razor Page view create a simple HTML form that presents the user with an input element containing the value of the `Price` property for a `Product` object. The `form` element is defined without an `action` attribute, which means the browser will send a `POST` request to the Razor Page’s URL when the user clicks the *Submit* button.

> ![[zICO - Warning - 16.png]] The `@Html.AntiForgeryToken()` expression in Listing 23-16 adds a hidden form field to the HTML form that ASP.NET Core uses to guard against cross-site request forgery (CSRF) attacks. I explain how this feature works in Chapter 27, but for this chapter, it is enough to know that `POST` requests that do not contain this form field will be rejected.

If you are using Visual Studio, expand the *Editor.cshtml* item in the Solution Explorer to reveal the *Editor.cshtml.cs* class file and replace its contents with the code shown in Listing 23-17. If you are using Visual Studio Code, add a file named Editor.cshtml.cs to the *WebApp/Pages* folder and use it to define the class shown in Listing 23-17.

Listing 23-17. The Contents of the Editor.cshtml.cs File in the Pages Folder
```cs
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using WebApp.Models;

namespace WebApp.Pages 
{
	public class EditorModel : PageModel 
	{
		private DataContext context;
		
		public Product? Product { get; set; }
		
		public EditorModel(DataContext ctx) 
		{
			context = ctx;
		}
		
		public async Task OnGetAsync(long id) 
		{
			Product = await context.Products.FindAsync(id);
		}
		
		public async Task<IActionResult> OnPostAsync(long id, decimal price) 
		{
			Product? p = await context.Products.FindAsync(id);
			if (p != null) 
			{
				p.Price = price;
			}
			
			await context.SaveChangesAsync();
			return RedirectToPage();
		}
	}
}
```

The page model class defines two handler methods, and the name of the method tells the Razor Pages framework which HTTP method each handles. 
The `OnGetAsync` method is used to handle `GET` requests, which it does by locating a `Product`, whose details are displayed by the view. 
The `OnPostAsync` method is used to handle `POST` requests, which will be sent by the browser when the user submits the HTML form. The parameters for the `OnPostAsync` method are obtained from the request so that the `id` value is obtained from the URL route and the price value is obtained from the form. (The model binding feature that extracts data from forms is described in Chapter 28.)

## ![[zICO - Warning - 16.png]] UNDERSTANDING THE POST REDIRECTION

Notice that the last statement in the `OnPostAsync` method invokes the `RedirectToPage` method without an argument, which redirects the client to the URL for the Razor Page. This may seem odd, but the effect is to tell the browser to send a `GET` request to the URL it used for the `POST` request. This type of redirection means that the browser won’t resubmit the `POST` request if the user reloads the browser, preventing the same action from being accidentally performed more than once.

To see how the page model class handles different HTTP methods, restart ASP.NET Core and use a browser to navigate to http://localhost:5000/editor/1. Edit the field to set the price to `100` and click the *Submit* button. The browser will send a `POST` request that is handled by the `OnPostAsync` method. The database will be updated, and the browser will be redirected so that the updated data is displayed, as shown in Figure 23-11.

Figure 23-11. Handling multiple HTTP methods
![[zIMG-asp.net-razor-pages-23-11.png]]

# Selecting a Handler Method

The page model class can define multiple handler methods, allowing the request to select a method using a handler query string parameter or routing segment variable. To demonstrate this feature, add a Razor Page file named *HandlerSelector.cshtml* to the *Pages* folder with the content shown in Listing 23-18.

Listing 23-18. The Contents of the HandlerSelector.cshtml File in the Pages Folder
```html
@page
@model HandlerSelectorModel
@using Microsoft.AspNetCore.Mvc.RazorPages
@using Microsoft.EntityFrameworkCore

<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<div class="bg-primary text-white text-center m-2 p-2">Selector</div>
	<div class="m-2">
		<table class="table table-sm table-striped table-bordered">
			<tbody>
				<tr><th>Name</th><td>@Model.Product?.Name</td></tr>
				<tr><th>Price</th><td>@Model.Product?.Name</td></tr>
				<tr><th>Category</th><td>@Model.Product?.Category?.Name</td></tr>
				<tr><th>Supplier</th><td>@Model.Product?.Supplier?.Name</td></tr>
			</tbody>
		</table>
		<a href="/handlerselector" class="btn btn-primary">Standard</a>
		<a href="/handlerselector?handler=related" class="btn btn-primary">Related</a>
	</div>
</body>
</html>

@functions
{
	public class HandlerSelectorModel: PageModel 
	{
		private DataContext context;
		
		public Product? Product { get; set; }
		
		public HandlerSelectorModel(DataContext ctx) 
		{
			context = ctx;
		}
		
		public async Task OnGetAsync(long id = 1) 
		{
			Product = await context.Products.FindAsync(id);
		}
		
		public async Task OnGetRelatedAsync(long id = 1) 
		{
			Product = await context.Products
				.Include(p => p.Supplier)
				.Include(p => p.Category)
				.FirstOrDefaultAsync(p => p.ProductId == id);
			
			if (Product != null && Product.Supplier != null) 
			{
				Product.Supplier.Products = null;
			}
			
			if (Product != null && Product.Category != null) 
			{
				Product.Category.Products = null;
			}
		}
	}
}
```

The page model class in this example defines two handler methods: `OnGetAsync` and `OnGetRelatedAsync`. 
The `OnGetAsync` method is used by default, which you can see by restarting ASP.NET Core and using a browser to request http://localhost:5000/handlerselector. The handler method queries the database and presents the result to the user, as shown on the left of Figure 23-12.
One of the anchor elements rendered by the page targets a URL with a handler query string parameter, like this:
```html
<a href="/handlerselector?handler=related" class="btn btn-primary">Related</a>
```

The name of the handler method is specified without the `On[method]` prefix and without the `Async` suffix so that the `OnGetRelatedAsync` method is selected using a handler value of related. This alternative handler method includes related data in its query and presents additional data to the user, as shown on the right of Figure 23-12.
![[zIMG-asp.net-razor-pages-23-12.png]]
