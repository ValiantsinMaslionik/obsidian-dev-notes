#aspnet_core/tag_helpers

---

This chapter uses the WebApp project from Chapter 24. To prepare for this chapter, replace the contents of the Program.cs file with those in Listing 25-1, removing some of the configuration statements used in earlier chapters.

Listing 25-1. The Contents of the Program.cs File in the WebApp Folder
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
builder.Services.AddRazorPages();
builder.Services.AddSingleton<CitiesData>();

var app = builder.Build();

app.UseStaticFiles();
app.MapControllers();
app.MapDefaultControllerRoute();
app.MapRazorPages();

var context = app.Services.CreateScope().ServiceProvider.GetRequiredService<DataContext>();
SeedData.SeedDatabase(context);

app.Run();
```

Next, replace the contents of the Index.cshtml file in the Views/Home folder with the content shown in Listing 25-2.

Listing 25-2. The Contents of the Index.cshtml File in the Views/Home Folder
```html
@model Product
@{
	Layout = "_SimpleLayout";
}
<table class="table table-striped table-bordered table-sm">
	<thead>
		<tr>
			<th colspan="2">Product Summary</th>
		</tr>
	</thead>
	<tbody>
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
	</tbody>
</table>
```

The view in Listing 25-2 relies on a new layout. Add a Razor View file named \_SimpleLayout.cshtml in the Views/Shared folder with the content shown in Listing 25-3.

Listing 25-3. The Contents of the _SimpleLayout.cshtml File in the Views/Shared Folder
```html
<!DOCTYPE html>
<html>
	<head>
		<title>@ViewBag.Title</title>
		<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
	</head>
	<body>
		<div class="m-2">@RenderBody()</div>
	</body>
</html>
```

## Dropping the Database

Open a new PowerShell command prompt, navigate to the folder that contains the WebApp.csproj file, and run the command shown in Listing 25-4 to drop the database.

Listing 25-4. Dropping the Database
`dotnet ef database drop --force`

## Running the Example Application

Use the PowerShell command prompt to run the command shown in Listing 25-5. Listing 25-5. Running the Example Application
`dotnet run`

# Creating a Tag Helper

The best way to understand tag helpers is to create one, which reveals how they operate and how they fit into an ASP.NET Core application. In the sections that follow, I go through the process of creating and applying a tag helper that will set the Bootstrap CSS classes for a `tr` element so that an element like this:
```html
<tr bg-color="primary">
	<th colspan="2">Product Summary</th>
</tr>
```

will be transformed into this:
```html
<tr class="bg-primary text-white text-center">
	<th colspan="2">Product Summary</th>
</tr>
```

The tag helper will recognize the `tr-color` attribute and use its value to set the class attribute on the element sent to the browser. This isn’t the most dramatic—or useful—transformation, but it provides a foundation for explaining how tag helpers work.

# Defining the Tag Helper Class

Tag helpers can be defined anywhere in the project, but it helps to keep them together because they need to be registered before they can be used. Create the *WebApp/TagHelpers* folder and add to it a class file named *TrTagHelper.cs* with the code shown in Listing 25-6.

Listing 25-6. The Contents of the TrTagHelper.cs File in the TagHelpers Folder ^a24b68
```cs
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	public class TrTagHelper: TagHelper 
	{
		public string BgColor { get; set; } = "dark";
		public string TextColor { get; set; } = "white";
		
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			output.Attributes.SetAttribute("class", $"bg-{BgColor} text-center text-{TextColor}");
		}
	}
}
```

Tag helpers are derived from the `TagHelper` class, which is defined in the `Microsoft.AspNetCore.Razor.TagHelpers` namespace. 
The `TagHelper` class defines a `Process` method, which is overridden by subclasses to implement the behavior that transforms elements.
The name of the tag helper combines the name of the element it transforms followed by *TagHelper*. In the case of the example, the class name `TrTagHelper` indicates this is a tag helper that operates on `tr` elements. The range of elements to which a tag helper can be applied can be broadened or narrowed using attributes, as described later in this chapter, but the default behavior is defined by the class name.

> Asynchronous tag helpers can be created by overriding the `ProcessAsync` method instead of the `Process` method, but this isn’t required for most helpers, which tend to make small and focused changes to HTML elements.

# Receiving Context Data

Tag helpers receive information about the element they are transforming through an instance of the `TagHelperContext` class, which is received as an argument to the `Process` method and which defines the properties described in Table 25-3.

Table 25-3. The TagHelperContext Properties

Name|Description
--|--
AllAttributes|This property returns a read-only dictionary of the attributes applied to the element being transformed, indexed by name and by index.
Items|This property returns a dictionary that is used to coordinate between tag helpers, as described in the “Coordinating Between Tag Helpers” section.
UniqueId|This property returns a unique identifier for the element being transformed.

Although you can access details of the element’s attributes through the `AllAttributes` dictionary, a more convenient approach is to define a property whose name corresponds to the attribute you are interested in, like this:
```cs
public string BgColor { get; set; } = "dark";
public string TextColor { get; set; } = "white";
```

When a tag helper is being used, the properties it defines are inspected and assigned the value of any whose name matches attributes applied to the HTML element. As part of this process, the attribute value will be converted to match the type of the C# property so that `bool` properties can be used to receive `true` and `false` attribute values and so `int` properties can be used to receive numeric attribute values such as `1` and `2`.

Properties for which there are no corresponding HTML element attributes are not set, which means you should check to ensure that you are not dealing with `null` or provide default values, which is the approach taken in [[#^a24b68|Listing 25-6]].

The name of the attribute is automatically converted from the default HTML style, `bg-color`, to the C# style, `BgColor`. You can use any attribute prefix except `asp-` (which Microsoft uses) and `data-` (which is reserved for custom attributes that are sent to the client). The example tag helper will be configured using `bg-color` and `text-color` attributes, which will provide values for the `BgColor` and `TextColor` properties and be used to configure the `tr` element in the `Process` method, as follows:
```cs
output.Attributes.SetAttribute("class", $"bg-{BgColor} text-center text-{TextColor}");
```

> Using the HTML attribute name for tag helper properties doesn’t always lead to readable or understandable classes. You can break the link between the name of the property and the attribute it represents using the `HtmlAttributeName` attribute, which can be used to specify the HTML attribute that the property represents.

# Producing Output

The `Process` method transforms an element by configuring the `TagHelperOutput` object that is received as an argument. The `TagHelperOuput` object starts by describing the HTML element as it appears in the view and is modified through the properties and methods described in Table 25-4.

Table 25-4. The TagHelperOutput Properties and Methods

Name|Description
--|--
TagName|This property is used to get or set the tag name for the output element.
Attributes|This property returns a dictionary containing the attributes for the output element.
Content|This property returns a `TagHelperContent` object that is used to set the content of the element.
GetChildContentAsync()|This asynchronous method provides access to the content of the element that will be transformed, as demonstrated in the “Creating Shorthand Elements” section.
PreElement|This property returns a `TagHelperContext` object that is used to insert content in the view before the output element. See the “Prepending and Appending Content and Elements” section.
PostElement|This property returns a `TagHelperContext` object that is used to insert content in the view after the output element. See the “Prepending and Appending Content and Elements” section.
PreContent|This property returns a `TagHelperContext` object that is used to insert content before the output element’s content. See the “Prepending and Appending Content and Elements” section.
PostContent|This property returns a `TagHelperContext` object that is used to insert content after the output element’s content. See the “Prepending and Appending Content and Elements” section.
TagMode|This property specifies how the output element will be written, using a value from the `TagMode` enumeration. See the “Creating Shorthand Elements” section.
SupressOuput()|Calling this method excludes an element from the view. See the “Suppressing the Output Element” section.

In the `TrTagHelper` class, I used the `Attributes` dictionary to add a class attribute to the HTML element that specifies Bootstrap styles, including the value of the `BgColor` and `TextColor` properties. The effect is that the background color for `tr` elements can be specified by setting `bg-color` and `text-color` attributes to Bootstrap names, such as `primary`, `info`, and `danger`.

# Registering Tag Helpers

Tag helper classes must be registered with the `@addTagHelper` directive before they can be used. The set of views or pages to which a tag helper can be applied depends on where the `@addTagHelper` directive is used.
For a single view or page, the directive appears in the CSHTML file itself. To make a tag helper available more widely, it can be added to the view imports file, which is defined in the *Views* folder for controllers and the *Pages* folder for Razor Pages.

I want the tag helpers that I create in this chapter to be available anywhere in the application, which means that the `@addTagHelper` directive is added to the \_ViewImports.cshtml files in the *Views* and *Pages* folders. **The `vc` element used in Chapter 24 to apply view components is a tag helper**, which is why the directive required to enable tag helpers is already in the \_ViewImports.cshtml file.

```html
@using WebApp.Models
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using WebApp.Components
@addTagHelper *, WebApp
```

The first part of the argument specifies the names of the tag helper classes, with support for wildcards, and the second part specifies the name of the assembly in which they are defined. This `@addTagHelper` directive uses the wildcard to select all namespaces in the *WebApp* assembly, with the effect that tag helpers defined anywhere in the project can be used in any controller view. There is an identical statement in the Razor Pages \_ViewImports.cshtml file in the *Pages* folder.
```html
@namespace WebApp.Pages
@using WebApp.Models
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper *, WebApp
```

The other `@addTagHelper` directive enables the built-in tag helpers that Microsoft provides, which are described in Chapter 26.

# Using a Tag Helper

The final step is to use the tag helper to transform an element. In Listing 25-7, I have added the attribute to the `tr` element, which will apply the tag helper.

Listing 25-7. Using a Tag Helper in the Index.cshtml File in the Views/Home Folder
```html
@model Product
@{
	Layout = "_SimpleLayout";
}
<table class="table table-striped table-bordered table-sm">
	<thead>
		<tr bg-color="info" text-color="white">
			<th colspan="2">Product Summary</th>
		</tr>
	</thead>
	<tbody>
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
	</tbody>
</table>
```

The `tr` element to which the attributes were applied in Listing 25-7 has been transformed, but that isn’t the only change shown in the figure. By default, tag helpers apply to all elements of a specific type, which means that all the `tr` elements in the view have been transformed using the default values defined in the tag helper class, since no attributes were defined. (The reason that some table rows show no text is because of the Bootstrap `table-striped` class, which applies different styles to alternate rows.)
In fact, the problem is more serious because the `@addTagHelper` directives in the view import files mean that the example tag helper is applied to all `tr` elements used in any view rendered by controllers and Razor Pages. Use a browser to request http://localhost:5000/cities, for example, and you will see the `tr` elements in the response from the Cities Razor Page have also been transformed, as shown in Figure 25-3.

Figure 25-3. Unexpectedly modifying elements with a tag helper
![[zIMG-asp.net-tag-helpers-25-3.png]]

## Narrowing the Scope of a Tag Helper

The range of elements that are transformed by a tag helper can be controlled using the `HtmlTargetElement` element, as shown in Listing 25-8.

Listing 25-8. Narrowing Scope in the TrTagHelper.cs File in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	[HtmlTargetElement("tr", Attributes = "bg-color,text-color", ParentTag = "thead")]
	public class TrTagHelper : TagHelper 
	{
		public string BgColor { get; set; } = "dark";
		public string TextColor { get; set; } = "white";
		
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			output.Attributes.SetAttribute("class", $"bg-{BgColor} text-center text-{TextColor}");
		}
	}
}
```

The `HtmlTargetElement` attribute describes the elements to which the tag helper applies. The first argument specifies the element type and supports the additional named properties described in Table 25-5.

Table 25-5. The HtmlTargetElement Properties

Name|Description
--|--
Attributes|This property is used to specify that a tag helper should be applied only to elements that have a given set of attributes, supplied as a comma-separated list. **An attribute name that ends with an asterisk** will be treated as a prefix so that `bg-*`` will match `bg-color`, `bgsize`, and so on.
ParentTag|This property is used to specify that a tag helper should be applied only to elements that are contained within an element of a given type.
TagStructure|This property is used to specify that a tag helper should be applied only to elements whose tag structure corresponds to the given value from the `TagStructure` enumeration, which defines `Unspecified`, `NormalOrSelfClosing`, and `WithoutEndTag`.

**The `Attributes` property supports CSS attribute selector syntax** so that `[bg-color]` matches elements that have a `bg-color` attribute, `[bg-color=primary]` matches elements that have a `bg-color` attribute whose value is `primary`, and `[bg-color^=p]` matches elements with a `bg-color` attribute whose value begins with `p`.

The attribute applied to the tag helper in Listing 25-8 matches `tr` elements with both `bg-color` and `text-color` attributes that are children of a `thead` element. Restart ASP.NET Core and use a browser to request http://localhost:5000/home/index/1, and you will see the scope of the tag helper has been narrowed, as shown in Figure 25-4.

Figure 25-4. Narrowing the scope of a tag helper
![[zIMG-asp.net-tag-helpers-25-4.png]]

## Widening the Scope of a Tag Helper

The `HtmlTargetElement` attribute can also be used to widen the scope of a tag helper so that it matches a broader range of elements. This is done by setting the attribute’s first argument to an *asterisk* (the * character), which matches any element. Listing 25-9 changes the attribute applied to the example tag helper so that it matches any element that has `bg-color` and `text-color` attributes.

Listing 25-9. Widening Scope in the TrTagHelper.cs File in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	[HtmlTargetElement("*", Attributes = "bg-color,text-color")]
	public class TrTagHelper : TagHelper 
	{
		public string BgColor { get; set; } = "dark";
		public string TextColor { get; set; } = "white";
		
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			output.Attributes.SetAttribute("class", $"bg-{BgColor} text-center text-{TextColor}");
		}
	}
}
```

Care must be taken when using the asterisk because it is easy to match too widely and select elements that should not be transformed. A safer middle ground is to apply the `HtmlTargetElement` attribute for each type of element, as shown in Listing 25-10.

Listing 25-10. Balancing Scope in the TrTagHelper.cs File in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	[HtmlTargetElement("tr", Attributes = "bg-color,text-color")]
	[HtmlTargetElement("td", Attributes = "bg-color")]
	public class TrTagHelper : TagHelper 
	{
		public string BgColor { get; set; } = "dark";
		public string TextColor { get; set; } = "white";
		
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			output.Attributes.SetAttribute("class", $"bg-{BgColor} text-center text-{TextColor}");
		}
	}
}
```

Each instance of the attribute can use different selection criteria. This tag helper matches `tr` elements with `bg-color` and `text-color` attributes and matches td elements with `bg-color` attributes. Listing 25-11 adds an element to be transformed to the Index view to demonstrate the revised scope.

Listing 25-11. Adding Attributes in the Index.cshtml File in the Views/Home Folder
```html
@model Product
@{
	Layout = "_SimpleLayout";
}

<table class="table table-striped table-bordered table-sm">
	<thead>
		<tr bg-color="info" text-color="white">
			<th colspan="2">Product Summary</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<th>Name</th>
			<td>@Model?.Name</td>
		</tr>
		<tr>
			<th>Price</th>
			<td bg-color="dark">@Model?.Price.ToString("c")</td>
		</tr>
		<tr>
			<th>Category ID</th>
			<td>@Model?.CategoryId</td>
		</tr>
	</tbody>
</table>
```

Restart ASP.NET Core and use a browser to request http://localhost:5000/home/index/1. The response will contain two transformed elements, as shown in Figure 25-5.

Figure 25-5. Managing the scope of a tag helper
![[zIMG-asp.net-tag-helpers-25-5.png]]

# Ordering tag helper execution

If you need to apply multiple tag helpers to an element, you can control the sequence in which they execute by setting the `Order` property, which is inherited from the `TagHelper` base class. Managing the sequence can help minimize the conflicts between tag helpers, although it is still easy to encounter problems.
