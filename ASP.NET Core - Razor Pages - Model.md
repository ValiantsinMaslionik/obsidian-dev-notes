#aspnet_core/razor_pages 

---

# Understanding the Page Model Class

Page models are derived from the `PageModel` class, which provides the link between the rest of ASP.NET Core and the view part of the Razor Page. The `PageModel` class provides methods for managing how requests are handled and properties that provide context data, the most useful of which are described in Table 23-3. I have listed these properties for completeness, but they are not often required in Razor Page development, which focuses more on selecting the data that is required to render the view part of the page.

Table 23-3. Selected PageModel Properties for Context Data
Name|Description
--|---
HttpContext|This property returns an HttpContext object, described in Chapter 12.
ModelState|This property provides access to the model binding and validation features described in Chapters 28 and 29.
PageContext|This property returns a PageContext object that provides access to many of the same properties defined by the `PageModel` class, along with additional information about the current page selection.
Request|This property returns an HttpRequest object that describes the current HTTP request, as described in Chapter [[ASP.NET Core - HttpRequest]].
Response|This property returns an HttpResponse object that represents the current response, as described in Chapter [[ASP.NET Core - HttpResponse]].
RouteData|This property provides access to the data matched by the routing system, as described in Chapter 13.
TempData|This property provides access to the temp data feature, which is used to store data until it can be read by a subsequent request. See [[ASP.NET Core - MVC - Views - ViewBags and TempData#Using Temp Data]] for details.
User|This property returns an object that describes the user associated with the request, as described in Chapter 38.

# Using a Code-Behind Class File

The `@function` directive allows the page-behind class and the Razor content to be defined in the same file, which is a development approach used by popular client-side frameworks, such as React or Vue.js. 
Defining code and markup in the same file is convenient but can become difficult to manage for more complex applications. Razor Pages can also be split into separate view and code files, which is similar to the MVC examples in previous chapters and is reminiscent of ASP.NET Web Pages, which defined C# classes in files known as code-behind files. 
The first step is to remove the page model class from the CSHTML file, as shown in Listing 23-9. I have also removed the `@using` expressions, which are no longer required.

Listing 23-9. Removing the Page Model Class in the Index.cshtml File in the Pages Folder
```html
@page "{id:long?}"
@model WebApp.Pages.IndexModel

<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<div class="bg-primary text-white text-center m-2 p-2">@Model.Product?.Name</div>
</body>
</html>
```

The `@model` expression has been modified to specify the namespace of the page model, which wasn’t required previously because the `@functions` expression defined the `IndexModel` class within the namespace of the view. When defining the separate page model class, I define the class in the `WebApp.Pages` namespace.
This isn’t a requirement, but it makes the C# class consistent with the rest of the application. The convention for naming Razor Pages code-behind files is to append the .cs file extension to the name of the view file. If you are using Visual Studio, the code-behind file was created by the Razor Page template when the *Index.cshtml* file was added to the project. Expand the *Index.cshtml* item in the Solution Explorer, and you will see the code-behind file, as shown in Figure 23-8. Open the file for editing and replace the contents with the statements shown in Listing 23-10.

If you are using Visual Studio Code, add a file named Index.cshtml.cs to the WebApp/Pages folder with the content shown in Listing 23-10.

Listing 23-10. The Contents of the Index.cshtml.cs File in the Pages Folder
```cs
using Microsoft.AspNetCore.Mvc.RazorPages;
using WebApp.Models;

namespace WebApp.Pages 
{
	public class IndexModel : PageModel 
	{
		private DataContext context;
		
		public Product? Product { get; set; }
		
		public IndexModel(DataContext ctx) 
		{
			context = ctx;
		}
		
		public async Task OnGetAsync(long id = 1) 
		{
			Product = await context.Products.FindAsync(id);
		}
	}
}
```

# Adding a View Imports File

A view imports file can be used to avoid using the fully qualified name for the page model class in the view file, performing the same role as the one I used in Chapter 22 for the MVC Framework. If you are using Visual Studio, use the Razor View Imports template to add a file named \_ViewImports.cshtml to the *WebApp/Pages* folder, with the content shown in Listing 23-11. If you are using Visual Studio Code, add the file directly.

Listing 23-11. The Contents of the _ViewImports.cshtml File in the WebApp/Pages Folder
```html
@namespace WebApp.Pages
@using WebApp.Models
```

The `@namespace` directive sets the namespace for the C# class that is generated by a view, and using the directive in the view imports file sets the default namespace for all the Razor Pages in the application, with the effect that the view and its page model class are in the same namespace and the `@model` directive does not require a fully qualified type, as shown in Listing 23-12.

Listing 23-12. Removing the Page Model Namespace in the Index.cshtml File in the Pages Folder
```html
@page "{id:long?}"
@model IndexModel

<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<div class="bg-primary text-white text-center m-2 p-2">@Model.Product?.Name</div>
</body>
</html>
```

Restart ASP.NET Core and use the browser to request http://localhost:5000/index, which will trigger the recompilation of the views. There is no difference in the response produced by the Razor Page, which is shown in Figure 23-9.

# Understanding Action Results in Razor Pages

Although it is not obvious, Razor Page handler methods use the same `IActionResult` interface to control the responses they generate. To make page model classes easier to develop, handler methods have an implied result that displays the view part of the page. Listing 23-13 makes the result explicit.

Listing 23-13. Using an Explicit Result in the Index.cshtml.cs File in the Pages Folder
```cs
using Microsoft.AspNetCore.Mvc.RazorPages;
using WebApp.Models;
using Microsoft.AspNetCore.Mvc;

namespace WebApp.Pages 
{
	public class IndexModel : PageModel 
	{
		private DataContext context;
		
		public Product? Product { get; set; }
		
		public IndexModel(DataContext ctx) 
		{
			context = ctx;
		}
		
		public async Task<IActionResult> OnGetAsync(long id = 1) 
		{
			Product = await context.Products.FindAsync(id);
			return Page();
		}
	}
}
```

==========

The Page method is inherited from the PageModel class and creates a PageResult object, which tells the
framework to render the view part of the page. Unlike the View method used in MVC action methods, the
Razor Pages Page method doesn’t accept arguments and always renders the view part of the page that has
been selected to handle the request.
The PageModel class provides other methods that create different action results to produce different
outcomes, as described in Table 23-4.
648
Chapter 23 ■ Using Razor Pages
Table 23-4. The PageModel Action Result Methods
Name Description
Page() This IActionResult returned by this method produces a 200 OK
status code and renders the view part of the Razor Page.
NotFound() The IActionResult returned by this method produces a 404 NOT
FOUND status code.
BadRequest(state) The IActionResult returned by this method produces a 400
BAD REQUEST status code. The method accepts an optional
model state object that describes the problem to the client, as
demonstrated in Chapter 19.
File(name, type) The IActionResult returned by this method produces a 200 OK
response, sets the Content-Type header to the specified type, and
sends the specified file to the client.
Redirect(path)
RedirectPermanent(path)
The IActionResult returned by these methods produces 302
FOUND and 301 MOVED PERMANENTLY responses, which
redirect the client to the specified URL.
RedirectToAction(name)RedirectTo
ActionPermanent(name)
The IActionResult returned by these methods produces 302
FOUND and 301 MOVED PERMANENTLY responses, which
redirect the client to the specified action method. The URL used
to redirect the client is produced using the routing features
described in Chapter 13.
RedirectToPage(name)
RedirectToPagePermanent(name)
The IActionResult returned by these methods produce 302
FOUND and 301 MOVED PERMANENTLY responses that redirect
the client to another Razor Page. If no name is supplied, the client
is redirected to the current page.
StatusCode(code) The IActionResult returned by this method produces a response
with the specific status code.
Using an Action Result
Except for the Page method, the methods in Table 23-4 are the same as those available in action methods.
However, care must be taken with these methods because sending a status code response is unhelpful in
Razor Pages because they are used only when a client expects the content of the view.
Instead of using the NotFound method when requested data cannot be found, for example, a better
approach is to redirect the client to another URL that can display an HTML message for the user. The
redirection can be to a static HTML file, to another Razor Page, or to an action defined by a controller. Add a
Razor Page named NotFound.cshtml to the Pages folder and add the content shown in Listing 23-14.
Listing 23-14. The Contents of the NotFound.cshtml File in the Pages Folder
@page "/noid"
@model NotFoundModel
@using Microsoft.AspNetCore.Mvc.RazorPages
@using WebApp.Models;
649
Chapter 23 ■ Using Razor Pages
<!DOCTYPE html>
<html>
<head>
<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
<title>Not Found</title>
</head>
<body>
<div class="bg-primary text-white text-center m-2 p-2">No Matching ID</div>
<ul class="list-group m-2">
@foreach (Product p in Model.Products) {
<li class="list-group-item">@p.Name (ID: @p.ProductId)</li>
}
</ul>
</body>
</html>
@functions {
public class NotFoundModel: PageModel {
private DataContext context;
public IEnumerable<Product> Products { get; set; }
= Enumerable.Empty<Product>();
public NotFoundModel(DataContext ctx) {
context = ctx;
}
public void OnGetAsync(long id = 1) {
Products = context.Products;
}
}
}
The @page directive overrides the route convention so that this Razor Page will handle the /noid URL
path. The page model class uses an Entity Framework Core context object to query the database and displays
a list of the product names and key values that are in the database.
In Listing 23-15, I have updated the handle method of the IndexModel class to redirect the user to the
NotFound page when a request is received that doesn’t match a Product object in the database.
Listing 23-15. Using a Redirection in the Index.cshtml.cs File in the Pages Folder
using Microsoft.AspNetCore.Mvc.RazorPages;
using WebApp.Models;
using Microsoft.AspNetCore.Mvc;
namespace WebApp.Pages {
public class IndexModel : PageModel {
private DataContext context;
public Product? Product { get; set; }
650
Chapter 23 ■ Using Razor Pages
public IndexModel(DataContext ctx) {
context = ctx;
}
public async Task<IActionResult> OnGetAsync(long id = 1) {
Product = await context.Products.FindAsync(id);
if (Product == null) {
return RedirectToPage("NotFound");
}
return Page();
}
}
}
The RedirectToPage method produces an action result that redirects the client to a different Razor
Page. The name of the target page is specified without the file extension, and any folder structure is
specified relative to the Pages folder. To test the redirection, restart ASP.NET Core and request http://
localhost:5000/index/500, which provides a value of 500 for the id segment variable and does not match
anything in the database. The browser will be redirected and produce the result shown in Figure 23-10.
Figure 23-10. Redirecting to a different Razor Page
Notice that the routing system is used to produce the URL to which the client is redirected, which uses
the routing pattern specified with the @page directive. In this example, the argument to the RedirectToPage
method was NotFound, but this has been translated into a redirection to the /noid path specified by the @
page directive in Listing 23-14.
651
Chapter 23 ■ Using Razor Pages
Handling Multiple HTTP Methods
Razor Pages can define handler methods that respond to different HTTP methods. The most common
combination is to support the GET and POST methods that allow users to view and edit data. To
demonstrate, add a Razor Page called Editor.cshtml to the Pages folder and add the content shown in
Listing 23-16.
■■Note I have kept this example as simple as possible, but there are excellent ASP.NET Core features for
creating HTML forms and for receiving data when it is submitted, as described in Chapter 31.
Listing 23-16. The Contents of the Editor.cshtml File in the WebApps/Pages Folder
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
<input name="price" class="form-control"
value="@Model.Product?.Price" />
</div>
<button class="btn btn-primary mt-2" type="submit">Submit</button>
</form>
</div>
</body>
</html>
The elements in the Razor Page view create a simple HTML form that presents the user with an input
element containing the value of the Price property for a Product object. The form element is defined
without an action attribute, which means the browser will send a POST request to the Razor Page’s URL
when the user clicks the Submit button.
652
Chapter 23 ■ Using Razor Pages
■■Note The @Html.AntiForgeryToken() expression in Listing 23-16 adds a hidden form field to the
HTML form that ASP.NET Core uses to guard against cross-site request forgery (CSRF) attacks. I explain how
this feature works in Chapter 27, but for this chapter, it is enough to know that POST requests that do not
contain this form field will be rejected.
If you are using Visual Studio, expand the Editor.cshtml item in the Solution Explorer to reveal the
Editor.cshtml.cs class file and replace its contents with the code shown in Listing 23-17. If you are using
Visual Studio Code, add a file named Editor.cshtml.cs to the WebApp/Pages folder and use it to define the
class shown in Listing 23-17.
Listing 23-17. The Contents of the Editor.cshtml.cs File in the Pages Folder
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using WebApp.Models;
namespace WebApp.Pages {
public class EditorModel : PageModel {
private DataContext context;
public Product? Product { get; set; }
public EditorModel(DataContext ctx) {
context = ctx;
}
public async Task OnGetAsync(long id) {
Product = await context.Products.FindAsync(id);
}
public async Task<IActionResult> OnPostAsync(long id, decimal price) {
Product? p = await context.Products.FindAsync(id);
if (p != null) {
p.Price = price;
}
await context.SaveChangesAsync();
return RedirectToPage();
}
}
}
The page model class defines two handler methods, and the name of the method tells the Razor Pages
framework which HTTP method each handles. The OnGetAsync method is used to handle GET requests,
which it does by locating a Product, whose details are displayed by the view.
The OnPostAsync method is used to handle POST requests, which will be sent by the browser when the
user submits the HTML form. The parameters for the OnPostAsync method are obtained from the request so
that the id value is obtained from the URL route and the price value is obtained from the form. (The model
binding feature that extracts data from forms is described in Chapter 28.)
653
Chapter 23 ■ Using Razor Pages
UNDERSTANDING THE POST REDIRECTION
Notice that the last statement in the OnPostAsync method invokes the RedirectToPage method
without an argument, which redirects the client to the URL for the Razor Page. This may seem odd, but
the effect is to tell the browser to send a GET request to the URL it used for the POST request. This type
of redirection means that the browser won’t resubmit the POST request if the user reloads the browser,
preventing the same action from being accidentally performed more than once.
To see how the page model class handles different HTTP methods, restart ASP.NET Core and use a
browser to navigate to http://localhost:5000/editor/1. Edit the field to set the price to 100 and click
the Submit button. The browser will send a POST request that is handled by the OnPostAsync method. The
database will be updated, and the browser will be redirected so that the updated data is displayed, as shown
in Figure 23-11.
Figure 23-11. Handling multiple HTTP methods
Selecting a Handler Method
The page model class can define multiple handler methods, allowing the request to select a method using a
handler query string parameter or routing segment variable. To demonstrate this feature, add a Razor Page
file named HandlerSelector.cshtml to the Pages folder with the content shown in Listing 23-18.
Listing 23-18. The Contents of the HandlerSelector.cshtml File in the Pages Folder
@page
@model HandlerSelectorModel
@using Microsoft.AspNetCore.Mvc.RazorPages
@using Microsoft.EntityFrameworkCore
654
Chapter 23 ■ Using Razor Pages
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
<a href="/handlerselector?handler=related" class="btn btn-primary">
Related
</a>
</div>
</body>
</html>
@functions{
public class HandlerSelectorModel: PageModel {
private DataContext context;
public Product? Product { get; set; }
public HandlerSelectorModel(DataContext ctx) {
context = ctx;
}
public async Task OnGetAsync(long id = 1) {
Product = await context.Products.FindAsync(id);
}
public async Task OnGetRelatedAsync(long id = 1) {
Product = await context.Products
.Include(p => p.Supplier)
.Include(p => p.Category)
.FirstOrDefaultAsync(p => p.ProductId == id);
if (Product != null && Product.Supplier != null) {
Product.Supplier.Products = null;
}
655
Chapter 23 ■ Using Razor Pages
if (Product != null && Product.Category != null) {
Product.Category.Products = null;
}
}
}
}
The page model class in this example defines two handler methods: OnGetAsync and
OnGetRelatedAsync. The OnGetAsync method is used by default, which you can see by restarting ASP.NET
Core and using a browser to request http://localhost:5000/handlerselector. The handler method
queries the database and presents the result to the user, as shown on the left of Figure 23-12.
One of the anchor elements rendered by the page targets a URL with a handler query string parameter,
like this:
...
<a href="/handlerselector?handler=related" class="btn btn-primary">Related</a>
...
The name of the handler method is specified without the On[method] prefix and without the Async
suffix so that the OnGetRelatedAsync method is selected using a handler value of related. This alternative
handler method includes related data in its query and presents additional data to the user, as shown on the
right of Figure 23-12.
