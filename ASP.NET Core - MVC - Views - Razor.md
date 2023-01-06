#aspnet_core/MVC

---

## Overview

To produce an HTML response to a browser request, I need a _#Term/view_, which tells ASP.NET Core how to process the result
produced by the Index method into an HTML response that can be sent to the browser.

```cs
using Microsoft.AspNetCore.Mvc;

namespace FirstProject.Controllers {
	public class HomeController : Controller {
		public ViewResult Index() {
			return View("MyView");
		}
	}
}
```

> When I return a ViewResult object from an action method, I am instructing ASP.NET Core to _#Term/render_ a
view. I create the ViewResult by calling the View method, specifying the name of the view that I want to use,
which is MyView.

==Views are stored in the Views folder, organized into subfolders.==
Views that are associated with the Home controller, for example, are stored in a folder called Views/Home.
Views that are not specific to a single controller are stored in a folder called Views/Shared.

```html
@{
	Layout = null;
}

<!DOCTYPE html>
<html>
	<head>
		<meta name="viewport" content="width=device-width" />
		<title>Index</title>
	</head>
	<body>
		<div>
			Hello World (from the view)
		</div>
	</body>
</html>
```

The Razor expression _Layout_ #Term/Razor/Expression/Layout tells Razor that I chose not to use a layout, which is like a template
for the HTML that will be sent to the browser (and which I describe in Chapter 22 #TODO/AddLink ).

#Term/Razor/Expression/AT - (_@_) This is an expression that will be interpreted by _#Term/Razor_, which is the component that processes the
contents of views and generates HTML that is sent to the browser. Razor is a _#Term/view_engine_ (_view engine_), and the expressions
in views are known as _#Term/Razor/Expression_ (_Razor Expressions_).

## Creating a Razor View

To provide the MVC Framework with a view to display, create the *Views/Home* folder and add to it a file named *Index.cshtml* with the content shown in Listing 21-7. If you are using Visual Studio, create the view by right-clicking the Views/Home folder, selecting Add ➤ New Item from the pop-up menu, and selecting the Razor View - Empty item in the ASP.NET Core ➤ Web category, as shown in Figure 21-3.

> There is a menu item for creating views in the Add popup menu, but this relies on the Visual Studio scaffolding feature, which adds template content to create different types of view. I don’t rely on the scaffolding in this book and instead show you how to create views from scratch.
> 
Listing 21-7. The Contents of the Index.cshtml File in the Views/Home Folder
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
					<tr><th>Price</th><td>@Model?.Price.ToString("c")</td></tr>
				</tbody>
			</table>
		</div>
	</body>
</html>
```

The view file contains standard HTML elements that are styled using the Bootstrap CSS framework, which is applied through the class attribute. The key view feature is the ability to generate content using C# expressions.

I explain how these expressions work in the “Understanding the Razor Syntax” section, but for now, it is enough to know that these expressions insert the value of the `Name` and `Price` properties from the `Product` view model passed to the `View` method by the action method in Listing 21-5.

## Modifying a Razor View

The `dotnet watch` command detects and recompiles Razor Views automatically, meaning that the ASP.NET Core runtime doesn’t have to be restarted when views are edited. To demonstrate the recompilation process, Listing 21-8 adds new elements to the Index view.

Listing 21-8. Adding Elements in the Index.cshtml File in the Views/Home Folder
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
				<tr><th>Price</th><td>@Model?.Price.ToString("c")</td></tr>
				<tr><th>Category ID</th><td>@Model?.CategoryId</td></tr>
			</tbody>
		</table>
	</div>
	</body>
</html>
```

Save the changes to the view; the change will be detected, and the browser will be automatically reloaded to display the change, as shown in Figure 21-5.



## Working with Razor views

Razor Views contain HTML elements and C# expressions. Expressions are mixed in with the HTML elements and denoted with the `@` character, like this:
```html
<tr><th>Name</th><td>@Model?.Name</td></tr>
```

When the view is used to generate a response, the expressions are evaluated, and the results are included in the content sent to the client. This expression gets the name of the `Product` view model object provided by the action method and produces output like this:
```html
<tr><th>Name</th><td>Corner Flags</td></tr>
```

This transformation can seem like magic, but Razor is simpler than it first appears. Razor Views are converted into C# classes that inherit from the RazorPage class, which are then compiled like any other C# class.

### Seen the Compiled Output From Razor Views

By default, Razor Views are compiled directly into a DLL, and the generated C# classes are not written to the disk during the build process. You can see the generated classes, by adding the following setting to the WebApp.csproj file, which is accessed in Visual Studio by right-clicking the WebApp item in the Solution Explorer and selecting Edit Project File from the pop-up menu:
```xml
<PropertyGroup>
	<EmitCompilerGeneratedFiles>true</EmitCompilerGeneratedFiles>
</PropertyGroup>
```

Save the project file and build the project using the dotnet build command. The C# files generated from the Razor Views will be written to the *obj/Debug/net6.0/generated* folder.
Table 21-3 describes the most useful properties and methods defined by `RazorPage<T>`.

### Caching Responses

Responses from views can be cached by applying the `ResponseCache` attribute to action methods (or to the controller class, which caches the responses from all the action methods). See Chapter 17 for details of how response caching is enabled.

### Useful `RazorPage<T>` Members

Table 21-3. Useful `RazorPage<T>`` Members
Name|Description
--|--
Context|This property returns the `HttpContext` object for the current request.
ExecuteAsync()|This method is used to generate the output from the view.
Layout|This property is used to set the view layout, as described in Chapter 22.
Model|This property returns the view model passed to the `View` method by the action.
RenderBody()|This method is used in layouts to include content from a view, as described in Chapter 22.
RenderSection()|This method is used in layouts to include content from a section in a view, as described in Chapter 22.
TempData|This property is used to access the temp data feature, which is described in Chapter 22.
ViewBag|This property is used to access the view bag, which is described in Chapter 22.
ViewContext|This property returns a `ViewContext` object that provides context data.
ViewData|This property returns the view data, which I used for unit testing controllers in the *SportsStore* application.
Write(str)|This method writes a string, which will be safely encoded for use in HTML.
WriteLiteral(str)|This method writes a string without encoding it for safe use in HTML.

The expressions in the view are translated into calls to the `Write` method, which encodes the result of the expression so that it can be included safely in an HTML document. The `WriteLiteral` method is used to deal with the static HTML regions of the view, which don’t need further encoding. The result is a fragment like this from the CSHTML file:
```html
<tr><th>Name</th><td>@Model?.Name</td></tr>
```

This is converted into a series of C# statements like these in the ExecuteAsync method:
```cs
WriteLiteral("<th>Name</th><td>");
Write(Model?.Name);
WriteLiteral("</td></tr>");
```

When the `ExecuteAsync` method is invoked, the response is generated with a mix of the static HTML and the expressions contained in the view. The results from evaluating the expressions are written to the response, producing HTML like this:
```html
<th>Name</th><td>Kayak</td></tr>
```

In addition to the properties and methods inherited from the `RazorPage<T>` class, the generated view class defines the properties described in Table 21-4, some of which are used for features described in later chapters.

Table 21-4. The Additional View Class Properties
Name|Description
--|--
Component|This property returns a helper for working with view components, which is accessed through the `vc` tag helper described in Chapter 25.
Html|This property returns an implementation of the `IHtmlHelper` interface. This property is used to manage HTML encoding, as described in Chapter 22.
Json|This property returns an implementation of the `IJsonHelper` interface, which is used to encode data as JSON, as described in Chapter 22.
ModelExpressionProvider|This property provides access to expressions that select properties from the model, which is used through tag helpers, described in Chapters 25–27.
Url|This property returns a helper for working with URLs, as described in Chapter 26.

### Setting the View Model Type

The generated class for the Watersports.cshtml file is derived from `RazorPage<T>`, but Razor doesn’tknow what type will be used by the action method for the view model, so it has selected dynamic as the generic type  argument. This means that the `@Model` expression can be used with any property or method name, which is evaluated at runtime when a response is generated. To demonstrate what happens when a nonexistent member is used, add the content shown in Listing 21-14 to the Watersports.cshtml file.

Listing 21-14. Adding Content in the Watersports.cshtml File in the Views/Home Folder
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
			<tr><th>Tax Rate</th><td>@Model?.TaxRate</td></tr>
			</tbody>
		</table>
	</div>
</body>
</html>
```

Use a browser to request *http://localhost:5000*, and you will see the exception shown in Figure 21-8.
You may need to start ASP.NET Core to see this error because the dotnet watch command can be confused when it is unable to load the compiled view.

Figure 21-8. Using a nonexistent property in a view expression
![[zIMG-asp.net-mvc-views-21-8.png]]

To check expressions during development, the type of the Model object can be specified using the `model` keyword, as shown in Listing 21-15.

> It is easy to get the two terms confused. `Model`, with **an uppercase M**, is used in expressions to access the view model object provided by the action method, while `model`, with **a lowercase m**, is used to specify the type of the view model.

Listing 21-15. Declaring the Model Type in the Watersports.cshtml File in the Views/Home Folder
```html
@model WebApp.Models.Product
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
				<tr><th>Tax Rate</th><td>@Model?.TaxRate</td></tr>
			</tbody>
		</table>
	</div>
</body>
</html>
```

An error warning will appear in the editor after a few seconds, as Visual Studio or Visual Studio Code checks the view in the background, as shown in Figure 21-9. When the `dotnet watch` command is used, an error will be displayed in the browser, also shown in Figure 21-9. The compiler will also report an error if you build the project or use the `dotnet build` or `dotnet run` command.

Figure 21-9. An error warning in a view file
![[zIMG-asp.net-mvc-views-21-9.png]]

When the C# class for the view is generated, the view model type is used as the generic type argument for the base class, like this:
```cs
internal sealed class Views_Home_Watersports : RazorPage<WebApp.Models.Product> {
```

Specifying a view model type allows Visual Studio and Visual Studio Code to suggest property and method names as you edit views. Replace the nonexistent property with the one shown in Listing 21-16.

Listing 21-16. Replacing a Property in the Watersports.cshtml File in the Views/Home Folder
```html
@model WebApp.Models.Product
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
				<tr><th>Supplier ID</th><td>@Model?.SupplierId</td></tr>
			</tbody>
		</table>
	</div>
</body>
</html>
```

As you type, the editor will prompt you with the possible member names defined by the view model class.

### Using a View Imports File

When I declared the view model object at the start of the *Watersports.cshtml* file, I had to include the namespace that contains the class, like this:
```html
@model WebApp.Models.Product
```

By default, all types that are referenced in a Razor View must be qualified with a namespace. This isn’t a big deal when the only type reference is for the model object, but it can make a view more difficult to read when writing more complex Razor expressions such as the ones I describe later in this chapter.
You can specify a set of namespaces that should be searched for types by adding a *view imports* file to the project. The view imports file is placed in the *Views* folder and is named *_ViewImports.cshtml*.

> Files in the *Views* folder whose names begin with an *underscore* (the _ character) are not returned to the user, which allows the file name to differentiate between views that you want to render and the files that support them. View imports files and layouts (which I describe shortly) are prefixed with an underscore.

If you are using Visual Studio, right-click the *Views* folder in the Solution Explorer, select Add ➤ New Item from the pop-up menu, and select the Razor View Imports template from the ASP.NET Core category.
Visual Studio will automatically set the name of the file to *_ViewImports.cshtml*, and clicking the *Add* button will create the file, which will be empty. If you are using Visual Studio Code, simply select the *Views* folder and add a new file called *_ViewImports.cshtml*. Regardless of which editor you used, add the expression shown Listing 21-17.

Listing 21-17. The Contents of the *_ViewImports.cshtml* File in the Views Folder
```cs
@using WebApp.Models
```

The namespaces that should be searched for classes used in Razor Views are specified using the *@using* expression, followed by the namespace. In Listing 21-17, I have added an entry for the WebApp.Models namespace that contains the view model class used in the *Watersports.cshtml* view.

Now that the namespace is included in the view imports file, I can remove the namespace from the view, as shown in Listing 21-18.

> You can also add an *@using* expression to individual view files, which allows types to be used without namespaces in a single view.

Listing 21-18. Simplifying the Model Type in the Watersports.cshtml File in the Views/Home Folder
```html
@model Product
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
				<tr><th>Supplier ID</th><td>@Model?.SupplierId</td></tr>
			</tbody>
		</table>
	</div>
</body>
</html>
```

## Dynamic Output

```cs
using Microsoft.AspNetCore.Mvc;

namespace FirstProject.Controllers {
	public class HomeController : Controller {
		public ViewResult Index() {
			int hour = DateTime.Now.Hour;
			string viewModel = hour < 12 ? "Good Morning" : "Good Afternoon";
			return View("MyView", viewModel);
		}
	}
}
```
```html
@model string

@{
	Layout = null;
}

<!DOCTYPE html>
<html>
	<head>
		<meta name="viewport" content="width=device-width" />
		<title>Index</title>
	</head>
	<body>
		<div>
			@Model World (from the view)
		</div>
	</body>
</html>
```

>The type of the view model is specified using the _@model_ #Term/Razor_Expressions/model expression, with a lowercase m. 
The view model value is included in the HTML output using the _@Model_ expression, with an uppercase M.

## Linking Action Methods

I want to be able to create a link from the Index view so that guests can see the RsvpForm view without having
to know the URL that targets a specific action method

```html
@{
	Layout = null;
}
<!DOCTYPE html>
<html>
	<head>
		<meta name="viewport" content="width=device-width" />
		<title>Party!</title>
	</head>
	<body>
		<div>
			<div>
				We're going to have an exciting party.<br />
				(To do: sell it better. Add pictures or something.)
			</div>
			<a asp-action="RsvpForm">RSVP Now</a>
		</div>
	</body>
</html>
```

The addition to the listing is an a element that has an _asp-action_ attribute. The attribute is an example
of a _#Term/Razor/TagHelper_ (tag helper) attribute, which is an instruction for Razor that will be performed when the view is rendered.

The _#Term/Razor/TagHelper/asp-action_ (_asp-action_) attribute is an instruction to add an href attribute to the a element that contains a URL for
an action method. I explain how tag helpers work in Chapters 25–27 #TODO/AddLink , but this tag helper tells Razor to insert a
URL for an action method defined by the same controller for which the current view is being rendered.

## Building the Form

```html
@model PartyInvites.Models.GuestResponse

@{
	Layout = null;
}
<!DOCTYPE html>
	<html>
		<head>
			<meta name="viewport" content="width=device-width" />
			<title>RsvpForm</title>
		</head>
	<body>
		<form asp-action="RsvpForm" method="post">
			<div>
				<label asp-for="Name">Your name:</label>
				<input asp-for="Name" />
			</div>
			<div>
				<label asp-for="Email">Your email:</label>
				<input asp-for="Email" />
			</div>
			<div>
				<label asp-for="Phone">Your phone:</label>
				<input asp-for="Phone" />
			</div>
			<div>
				<label asp-for="WillAttend">Will you attend?</label>
				<select asp-for="WillAttend">
				<option value="">Choose an option</option>
				<option value="true">Yes, I'll be there</option>
				<option value="false">No, I can't come</option>
				</select>
			</div>
			<button type="submit">Submit RSVP</button>
		</form>
	</body>
</html>
```

The @model expression specifies that the view expects to receive a GuestResponse object as its view
model. I have defined a label and input element for each property of the GuestResponse model class (or, in
the case of the WillAttend property, a select element). 

Each element is associated with the model property using the #Term/Razor/TagHelper/asp-for  (_asp-for_) attribute, which is another tag helper attribute. The tag helper attributes configure the elements to tie them to the view model object. 
Here is an example of the HTML that the tag helpers produce:
```html
<p>
	<label for="Name">Your name:</label>
	<input type="text" id="Name" name="Name" value="">
</p>
```

The asp-for attribute on the label element sets the value of the for attribute. The asp-for attribute on
the input element sets the id and name elements. This may not look especially useful, but you will see that
associating elements with a model property offers additional advantages as the application functionality is defined.

Of more immediate use is the asp-action attribute applied to the form element, which uses the application’s
[[ASP.NET Core - Routing|URL routing]] configuration to set the action attribute to a URL that will target a specific [[ASP.NET Core - MVC - Controllres and Actions#^8ffa5d|action]] method, like this:
```html
<form method="post" action="/Home/RsvpForm">
```

==As with the helper attribute I applied to the a element, the benefit== of this approach is that when you can
change the system of URLs that the application uses, the content generated by the tag helpers will reflect the
changes automatically.

