#aspnet_core/tag_helpers 

---

# Creating Shorthand Elements

Tag helpers are not restricted to transforming the standard HTML elements and can also be used to replace custom elements with commonly used content. This can be a useful feature for making views more concise and making their intent more obvious. To demonstrate, Listing 25-12 replaces the `thead` element in the *Index* view with a custom HTML element.

Listing 25-12. Adding a Custom HTML Element in the Index.cshtml File in the Views/Home Folder
```html
@model Product
@{
	Layout = "_SimpleLayout";
}

<table class="table table-striped table-bordered table-sm">
	<tablehead bg-color="dark">Product Summary</tablehead>
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

The `tablehead` element isn’t part of the HTML specification and won’t be understood by browsers. Instead, I am going to use this element as shorthand for generating the `thead` element and its content for the HTML table. Add a class named *TableHeadTagHelper.cs* to the *TagHelpers* folder and use it to define the class shown in Listing 25-13.

> ![[zICO - Warning - 16.png]] When dealing with custom elements that are not part of the HTML specification, you must apply the `HtmlTargetElement` attribute and specify the element name, as shown in Listing 25-13. The convention of applying tag helpers to elements based on the class name works only for standard element names.

Listing 25-13. The Contents of TableHeadTagHelper.cs in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	[HtmlTargetElement("tablehead")]
	public class TableHeadTagHelper: TagHelper 
	{
		public string BgColor { get; set; } = "light";
		
		public override async Task ProcessAsync(TagHelperContext context, TagHelperOutput output) 
		{
			output.TagName = "thead";
			output.TagMode = TagMode.StartTagAndEndTag;
			output.Attributes.SetAttribute("class", $"bg-{BgColor} text-white text-center");
			
			string content = (await output.GetChildContentAsync()).GetContent();
			output.Content.SetHtmlContent($"<tr><th colspan=\"2\">{content}</th></tr>");
		}
	}
}
```

This tag helper is asynchronous and overrides the `ProcessAsync` method so that it can access the existing content of the elements it transforms. The `ProcessAsync` method uses the properties of the `TagHelperOuput` object to generate a completely different element: the `TagName` property is used to specify a `thead` element, the `TagMode` property is used to specify that the element is written using start and end tags, the `Attributes.SetAttribute` method is used to define a `class` attribute, and the `Content` property is used to set the element content.
**The existing content of the element is obtained** through the asynchronous `GetChildContentAsync` method, which returns a `TagHelperContent` object. This is the same object that is returned by the `TagHelperOutput.Content` property and allows the content of the element to be inspected and changed using the same type, through the methods described in Table 25-6.

Table 25-6. Useful TagHelperContent Methods

Name|Description
--|--
GetContent()|This method returns the contents of the HTML element as a string.
SetContent(text)|This method sets the content of the output element. The string argument is encoded so that it is safe for inclusion in an HTML element.
SetHtmlContent(html)|This method sets the content of the output element. The string argument is assumed to be safely encoded. **Use with caution.**
Append(text)|This method safely encodes the specified string and adds it to the content of the output element.
AppendHtml(html)|This method adds the specified string to the content of the output element without performing any encoding. **Use with caution.**
Clear()|This method removes the content of the output element.

In Listing 25-13, the existing content of the element is read through the `GetContent` element and then set using the `SetHtmlContent` method. The effect is to wrap the existing content in the transformed element in `tr` and `th` elements.

The tag helper transforms this shorthand element:
```html
<tablehead bg-color="dark">Product Summary</tablehead>
```

into these elements:
```html
<thead class="bg-dark text-white text-center">
	<tr>
		<th colspan="2">Product Summary</th>
	</tr>
</thead>
```

Notice that the transformed elements do not include the `bg-color` attribute. **Attributes matched to properties defined by the tag helper are removed from the output element and must be explicitly redefined if they are required.**

# Creating Elements Programmatically

When generating new HTML elements, you can use standard C# string formatting to create the content you require, which is the approach I took in Listing 25-13. This works, but it can be awkward and requires close attention to avoid typos. A more robust approach is to use the `TagBuilder` class, which is defined in the `Microsoft.AspNetCore.Mvc.Rendering` namespace and allows elements to be created in a more structured manner. The `TagHelperContent` methods described in Table 25-6 accept `TagBuilder` objects, which makes it easy to create HTML content in tag helpers, as shown in Listing 25-14.

Listing 25-14. Creating HTML Elements in the TableHeadTagHelper.cs File in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Razor.TagHelpers;
using Microsoft.AspNetCore.Mvc.Rendering;

namespace WebApp.TagHelpers 
{
	[HtmlTargetElement("tablehead")]
	public class TableHeadTagHelper: TagHelper 
	{
		public string BgColor { get; set; } = "light";
		
		public override async Task ProcessAsync(TagHelperContext context, TagHelperOutput output) 
		{
			output.TagName = "thead";
			output.TagMode = TagMode.StartTagAndEndTag;
			output.Attributes.SetAttribute("class", $"bg-{BgColor} text-white text-center");
			
			string content = (await output.GetChildContentAsync()).GetContent();
			
			TagBuilder header = new TagBuilder("th");
			header.Attributes["colspan"] = "2";
			header.InnerHtml.Append(content);
			
			TagBuilder row = new TagBuilder("tr");
			row.InnerHtml.AppendHtml(header);
			
			output.Content.SetHtmlContent(row);
		}
	}
}
```

This example creates each new element using a `TagBuilder` object and composes them to produce the same HTML structure as the string-based version in Listing 25-13.

# Prepending and Appending Content and Elements

The `TagHelperOutput` class provides four properties that make it easy to inject new content into a view so that it surrounds an element or the element’s content, as described in Table 25-7. In the sections that follow, I explain how you can insert content around and inside the target element.

Table 25-7. The TagHelperOutput Properties for Appending Context and Elements

Name|Description
--|--
PreElement|This property is used to insert elements into the view before the target element.
PostElement|This property is used to insert elements into the view after the target element.
PreContent|This property is used to insert content into the target element, before any existing content.
PostContent|This property is used to insert content into the target element, after any existing content.

# Inserting Content Around the Output Element

The first `TagHelperOuput` properties are `PreElement` and `PostElement`, which are used to insert elements into the view before and after the output element. To demonstrate the use of these properties, add a class file named *ContentWrapperTagHelper.cs* to the *WebApp/TagHelpers* folder with the content shown in Listing 25-15.

Listing 25-15. The Contents of the WrapperTagHelper.cs File in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Mvc.Rendering;
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	[HtmlTargetElement("*", Attributes = "[wrap=true]")]
	public class ContentWrapperTagHelper: TagHelper 
	{
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			TagBuilder elem = new TagBuilder("div");
			elem.Attributes["class"] = "bg-primary text-white p-2 m-2";
			elem.InnerHtml.AppendHtml("Wrapper");
			
			output.PreElement.AppendHtml(elem);
			output.PostElement.AppendHtml(elem);
		}
	}
}
```

This tag helper transforms elements that have a `wrap` attribute whose value is `true`, which it does using the `PreElement` and `PostElement` properties to add a `div` element before and after the output element.
Listing 25-16 adds an element to the Index view that is transformed by the tag helper.

Listing 25-16. Adding an Element in the Index.cshtml File in the Views/Home Folder
```html
@model Product
@{
	Layout = "_SimpleLayout";
}
<div class="m-2" wrap="true">Inner Content</div>

<table class="table table-striped table-bordered table-sm">
	<tablehead bg-color="dark">Product Summary</tablehead>
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

Restart ASP.NET Core and use a browser to request http://localhost:5000/home/index/1. The response includes the transformed element, as shown in Figure 25-7.

Figure 25-7. Inserting content around the output element
![[zIMG-asp.net-tag-helpers-25-7.png]]

If you examine the HTML sent to the browser, you will see that this element:
```html
<div class="m-2" wrap="true">Inner Content</div>
```

has been transformed into these elements:
```html
<div class="bg-primary text-white p-2 m-2">Wrapper</div>
<div class="m-2" wrap="true">Inner Content</div>
<div class="bg-primary text-white p-2 m-2">Wrapper</div>
```

**Notice that the `wrap` attribute has been left on the output element.** This is because I didn’t define a property in the tag helper class that corresponds to this attribute. **If you want to prevent attributes from being included in the output, then define a property for them in the tag helper class, even if you don’t use the attribute value.**

# Inserting Content Inside the Output Element

The `PreContent` and `PostContent` properties are used to insert content inside the output element, surrounding the original content. To demonstrate this feature, add a class file named *HighlightTagHelper.cs* to the `TagHelpers` folder and use it to define the tag helper shown in Listing 25-17.

Listing 25-17. The Contents of the HighlightTagHelper.cs File in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	[HtmlTargetElement("*", Attributes = "[highlight=true]")]
	public class HighlightTagHelper: TagHelper 
	{
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			output.PreContent.SetHtmlContent("<b><i>");
			output.PostContent.SetHtmlContent("</i></b>");
		}
	}
}
```

This tag helper inserts `b` and `i` elements around the output element’s content. Listing 25-18 adds the `wrap` attribute to one of the table cells in the Index view.

Listing 25-18. Adding an Attribute in the Index.cshtml File in the Views/Home Folder
```html
@model Product
@{
	Layout = "_SimpleLayout";
}
<div class="m-2" wrap="true">Inner Content</div>

<table class="table table-striped table-bordered table-sm">
	<tablehead bg-color="dark">Product Summary</tablehead>
	<tbody>
		<tr>
			<th>Name</th>
			<td highlight="true">@Model?.Name</td>
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

Restart ASP.NET Core and use a browser to request http://localhost:5000/home/index/1. The response includes the transformed element, as shown in Figure 25-8.

Figure 25-8. Inserting content inside an element
![[zIMG-asp.net-tag-helpers-25-8.png]]

If you examine the HTML sent to the browser, you will see that this element:
```html
<td highlight="true">@Model?.Name</td>
```

has been transformed into these elements:
```html
<td highlight="true"><b><i>Kayak</i></b></td>
```

# Getting View Context Data

A common use for tag helpers is to transform elements so they contain details of the current request or the view model/page model, which requires access to context data. To create this type of tag helper, add a file named *RouteDataTagHelper.cs* to the *TagHelpers* folder, with the content shown in Listing 25-19.

Listing 25-19. The Contents of the RouteDataTagHelper.cs File in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Mvc.Rendering;
using Microsoft.AspNetCore.Mvc.ViewFeatures;
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	[HtmlTargetElement("div", Attributes = "[route-data=true]")]
	public class RouteDataTagHelper : TagHelper 
	{
		[ViewContext]
		[HtmlAttributeNotBound]
		public ViewContext Context { get; set; } = new();
		
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			output.Attributes.SetAttribute("class", "bg-primary m-2 p-2");
			
			TagBuilder list = new TagBuilder("ul");
			list.Attributes["class"] = "list-group";
			
			RouteValueDictionary rd = Context.RouteData.Values;
			if (rd.Count > 0) 
			{
				foreach (var kvp in rd) 
				{
					TagBuilder item = new TagBuilder("li");
					item.Attributes["class"] = "list-group-item";
					item.InnerHtml.Append($"{kvp.Key}: {kvp.Value}");
					list.InnerHtml.AppendHtml(item);
				}
				
				output.Content.AppendHtml(list);
			} 
			else 
			{
				output.Content.Append("No route data");
			}
		}
	}
}
```

The tag helper transforms `div` elements that have a `route-data` attribute whose value is `true` and populates the output element with a list of the segment variables obtained by the routing system.
To get the route data, I added a property called `Context` and decorated it with two attributes, like this:
```cs
[ViewContext]
[HtmlAttributeNotBound]
public ViewContext Context { get; set; } = new();
```

The `ViewContext` attribute denotes that the value of this property should be assigned a `ViewContext` object when a new instance of the tag helper class is created, which provides details of the view that is being rendered, including the routing data, as described in Chapter 13.
The `HtmlAttributeNotBound` attribute prevents a value from being assigned to this property if there is a matching attribute defined on the `div` element. This is good practice, especially if you are writing tag helpers for other developers to use.

> Tag helpers can declare dependencies on services in their constructors, which are resolved using the dependency injection feature described in Chapter 14.

Listing 25-20 adds an element to the Home controller’s *Index* view that will be transformed by the new tag helper.

Listing 25-20. Adding an Element in the Index.cshtml File in the Views/Home Folder
```html
@model Product
@{
Layout = "_SimpleLayout";
}

<div route-data="true"></div>

<table class="table table-striped table-bordered table-sm">
	<tablehead bg-color="dark">Product Summary</tablehead>
	<tbody>
		<tr>
			<th>Name</th>
			<td highlight="true">@Model?.Name</td>
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

Restart ASP.NET Core and use a browser to request http://localhost:5000/home/index/1. The response will include a list of the segment variables the routing system has matched, as shown in Figure 25-9.

Figure 25-9. Displaying context data with a tag helper
![[zIMG-asp.net-tag-helpers-25-9.png]]

# Working with Model Expressions

Tag helpers can operate on the view model, tailoring the transformations they perform or the output they create. To see how this feature works, add a class file named *ModelRowTagHelper.cs* to the *TagHelpers* folder, with the code shown in Listing 25-21.

Listing 25-21. The Contents of the ModelRowTagHelper.cs File in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Mvc.Rendering;
using Microsoft.AspNetCore.Mvc.ViewFeatures;
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	[HtmlTargetElement("tr", Attributes = "for")]
	public class ModelRowTagHelper : TagHelper 
	{
		public string Format { get; set; } = "";
		public ModelExpression? For { get; set; }
		
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			output.TagMode = TagMode.StartTagAndEndTag;
			
			TagBuilder th = new TagBuilder("th");
			th.InnerHtml.Append(For?.Name ?? String.Empty);
			output.Content.AppendHtml(th);
			
			TagBuilder td = new TagBuilder("td");
			if (Format != null && For?.Metadata.ModelType == typeof(decimal)) 
			{
				td.InnerHtml.Append(((decimal)For.Model).ToString(Format));
			}
			else
			{
				td.InnerHtml.Append(For?.Model.ToString() ?? String.Empty);
			}
			
			output.Content.AppendHtml(td);
		}
	}
}
```

This tag helper transforms tr elements that have a `for` attribute. The important part of this tag helper is the type of the `For` property, which is used to receive the value of the for attribute.
```cs
public ModelExpression? For { get; set; }
```

The `ModelExpression` class is used when you want to operate on part of the view model, which is most easily explained by jumping forward and showing how the tag helper is applied in the view, as shown in Listing 25-22.

> ![[zICO - Warning - 16.png]] The `ModelExpression` feature can be used only on view models or page models. It cannot be used on variables that are created within a view, such as with an `@foreach` expression.

Listing 25-22. Using the Tag Helper in the Index.cshtml File in the Views/Home Folder
```html
@model Product
@{
	Layout = "_SimpleLayout";
}
<div route-data="true"></div>
<table class="table table-striped table-bordered table-sm">
	<tablehead bg-color="dark">Product Summary</tablehead>
	<tbody>
		<tr for="Name" />
		<tr for="Price" format="c" />
		<tr for="CategoryId" />
	</tbody>
</table>
```

The value of the `for` attribute is the name of a property defined by the view model class. When the tag helper is created, the type of the `For` property is detected and assigned a `ModelExpression` object that describes the selected property. I am not going to describe the `ModelExpression` class in any detail because any introspection on types leads to endless lists of classes and properties. Further, ASP.NET Core provides a useful set of built-in tag helpers that use the view model to transform elements, as described in Chapter 26, which means you don’t need to create your own.

For the example tag helper, I use three basic features that are worth describing. 
The first is to get the name of the model property so that I can include it in the output element, like this:
```cs
// The `Name` property returns the name of the model property.
th.InnerHtml.Append(For?.Name ?? String.Empty);
```

The second feature is to get the type of the model property so that I can determine whether to format the value, like this:
```cs
if (Format != null && For?.Metadata.ModelType == typeof(decimal)) {
```

The third feature is to get the value of the property so that it can be included in the response.
```cs
td.InnerHtml.Append(For?.Model.ToString() ?? String.Empty);
```

Restart ASP.NET Core and use a browser to request http://localhost:5000/home/index/2, and you will see the response shown in Figure 25-10.
Figure 25-10. Using the view model in a tag helper
![[zIMG-asp.net-tag-helpers-25-10.png]]

## Working with the Page Model

Tag helpers with model expressions can be applied in Razor Pages, although the expression that selects the property must account for the way that the `Model` property returns the page model class. Listing 25-23 applies the tag helper to the Editor Razor Page, whose page model defines a Product property.

Listing 25-23. Applying a Tag Helper in the Editor.cshtml File in the Pages Folder
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
		<div class="bg-primary text-white text-center m-2 p-2">Editor</div>
		<div class="m-2">
			<table class="table table-sm table-striped table-bordered">
				<tbody>
					<tr for="Product.Name" />
					<tr for="Product.Price" format="c" />
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

The value for the `for` attribute selects the nested properties through the `Product` property, which provides the tag helper with the `ModelExpression` it requires. **Model expressions cannot be used with the `null` conditional operator**, which presents a problem for this
example because the type of the `Product` property is `Product?`. Listing 25-24 changes the property type to `Product` and assigns a default value. (I demonstrate a different way of resolving this issue in Chapter 27.)

Listing 25-24. Changing a Property Type in the Editor.cshtml.cs File in the Pages Folder
```cs
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using WebApp.Models;

namespace WebApp.Pages 
{
	public class EditorModel : PageModel 
	{
		private DataContext context;
		
		public Product Product { get; set; } = new();
		
		public EditorModel(DataContext ctx) 
		{
			context = ctx;
		}
		
		public async Task OnGetAsync(long id) 
		{
			Product = await context.Products.FindAsync(id) ?? new();
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

One consequence of the page model is that the `ModelExpression.Name` property will return `Product.Name`, for example, instead of just Name. Listing 25-25 updates the tag helper so that it will display just the last part of the model expression name.
![[zIMG-asp.net-tag-helpers-25-11.png]]

> This example is intended to highlight the effect of the page model on model expressions. Instead of displaying just the last part of the name, a more flexible approach is to add support for another attribute that allows the display value to be overridden as needed.

Listing 25-25. Processing Names in the ModelRowTagHelper.cs File in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Mvc.Rendering;
using Microsoft.AspNetCore.Mvc.ViewFeatures;
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	[HtmlTargetElement("tr", Attributes = "for")]
	public class ModelRowTagHelper : TagHelper 
	{
		public string Format { get; set; } = "";
		public ModelExpression? For { get; set; }
		
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			output.TagMode = TagMode.StartTagAndEndTag;
			
			TagBuilder th = new TagBuilder("th");
			th.InnerHtml.Append(For?.Name.Split(".").Last() ?? String.Empty);
			output.Content.AppendHtml(th);
			
			TagBuilder td = new TagBuilder("td");
			if (Format != null && For?.Metadata.ModelType == typeof(decimal)) 
			{
				td.InnerHtml.Append(((decimal)For.Model).ToString(Format));
			}
			else 
			{
				td.InnerHtml.Append(For?.Model.ToString() ?? String.Empty);
			}
			output.Content.AppendHtml(td);
		}
	}
}
```

# Coordinating Between Tag Helpers

The `TagHelperContext.Items` property provides a dictionary used by tag helpers that operate on elements and those that operate on their descendants. To demonstrate the use of the Items collection, add a classfile named *CoordinatingTagHelpers.cs* to the *WebApp/TagHelpers* folder and add the code shown in Listing 25-26.

Listing 25-26. The Contents of the CoordinatingTagHelpers.cs File in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	[HtmlTargetElement("tr", Attributes = "theme")]
	public class RowTagHelper : TagHelper 
	{
		public string Theme { get; set; } = String.Empty;
		
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			context.Items["theme"] = Theme;
		}
	}
	
	[HtmlTargetElement("th")]
	[HtmlTargetElement("td")]
	public class CellTagHelper : TagHelper 
	{
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			if (context.Items.ContainsKey("theme")) 
			{
				output.Attributes.SetAttribute("class", $"bg-{context.Items["theme"]} text-white");
			}
		}
	}
}
```

The first tag helper operates on `tr` elements that have a `theme` attribute. Coordinating tag helpers can transform their own elements, but this example simply adds the value of the `theme` attribute to the `Items` dictionary so that it is available to tag helpers that operate on elements contained within the `tr` element. The second tag helper operates on `th` and `td` elements and uses the `theme` value from the `Items` dictionary to set the Bootstrap style for its output elements.
Listing 25-27 adds elements to the Home controller’s Index view that apply the coordinating tag helpers.

> ![[zICO - Warning - 16.png]] Notice that I have added the `th` and `td` elements that are transformed in Listing 25-27, instead of relying on a tag helper to generate them. **Tag helpers are not applied to elements generated by other tag helpers and affect only the elements defined in the view.**

Listing 25-27. Applying a Tag Helper in the Index.cshtml File in the Views/Home Folder
```html
@model Product
@{
	Layout = "_SimpleLayout";
}
<div route-data="true"></div>
<table class="table table-striped table-bordered table-sm">
	<tablehead bg-color="dark">Product Summary</tablehead>
	<tbody>
		<tr theme="primary">
			<th>Name</th>
			<td>@Model?.Name</td>
		</tr>
		<tr theme="secondary">
			<th>Price</th>
			<td>@Model?.Price.ToString("c")</td>
		</tr>
		<tr theme="info">
			<th>Category</th>
			<td>@Model?.CategoryId</td>
		</tr>
	</tbody>
</table>
```

Restart ASP.NET Core and use a browser to request http://localhost:5000/home, which produces the response shown in Figure 25-12. The value of the theme element has been passed from one tag helper to another, and a color theme is applied without needing to define attributes on each of the elements that is transformed.

Figure 25-12. Coordination between tag helpers
![[zIMG-asp.net-tag-helpers-25-12.pn]]

# Suppressing the Output Element

Tag helpers can be used to prevent an element from being included in the HTML response by calling the `SuppressOuput` method on the `TagHelperOutput` object that is received as an argument to the `Process` method. In Listing 25-28, I have added an element to the Home controller’s Index view that should be displayed only if the `Price` property of the view model exceeds a specified value.

Listing 25-28. Adding an Element in the Index.cshtml File in the Views/Home Folder
```html
@model Product
@{
	Layout = "_SimpleLayout";
}
<div show-when-gt="500" for="Price">
	<h5 class="bg-danger text-white text-center p-2">Warning: Expensive Item</h5>
</div>
<table class="table table-striped table-bordered table-sm">
	<tablehead bg-color="dark">Product Summary</tablehead>
	<tbody>
		<tr theme="primary">
		<th>Name</th>
			<td>@Model?.Name</td>
		</tr>
		<tr theme="secondary">
			<th>Price</th>
			<td>@Model?.Price.ToString("c")</td>
		</tr>
		<tr theme="info">
			<th>Category</th>
			<td>@Model?.CategoryId</td>
		</tr>
	</tbody>
</table>
```

The `show-when-gt` attribute specifies the value above which the `div` element should be displayed, and the `for` property selects the model property that will be inspected. To create the tag helper that will manage the elements, including the response, add a class file named *SelectiveTagHelper.cs* to the *WebApp/TagHelpers* folder with the code shown in Listing 25-29.

Listing 25-29. The Contents of the SelectiveTagHelper.cs File in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Mvc.ViewFeatures;
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	[HtmlTargetElement("div", Attributes = "show-when-gt, for")]
	public class SelectiveTagHelper : TagHelper 
	{
		public decimal ShowWhenGt { get; set; }
		public ModelExpression? For { get; set; }
		
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			if (For?.Model.GetType() == typeof(decimal) && (decimal)For.Model <= ShowWhenGt) 
			{
				output.SuppressOutput();
			}
		}
	}
}
```

The tag helper uses the model expression to access the property and calls the `SuppressOutput` method unless the threshold is exceeded.

# Using Tag Helper Components

Tag helper components provide an alternative approach to applying tag helpers as services. This feature can be useful when you need to set up tag helpers to support another service or middleware component, which is typically the case for diagnostic tools or functionality that has both a client-side component and a serverside component, such as Blazor, which is described in Part 4. In the sections that follow, I show you how to create and apply tag helper components.

## Creating a Tag Helper Component

Tag helper components are derived from the `TagHelperComponent` class, which provides a similar API to the `TagHelper` base class used in earlier examples. To create a tag helper component, add a class file called *TimeTagHelperComponent.cs* in the *TagHelpers* folder with the content shown in Listing 25-30.

Listing 25-30. The Contents of the TimeTagHelperComponent.cs File in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Mvc.Rendering;
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	public class TimeTagHelperComponent: TagHelperComponent 
	{
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			string timestamp = DateTime.Now.ToLongTimeString();
			if (output.TagName == "body") 
			{
				TagBuilder elem = new TagBuilder("div");
				elem.Attributes.Add("class", "bg-info text-white m-2 p-2");
				elem.InnerHtml.Append($"Time: {timestamp}");
				output.PreContent.AppendHtml(elem);
			}
		}
	}
}
```

Tag helper components do not specify the elements they transform, and the `Process` method is invoked for every element for which the tag helper component feature has been configured. By default, tag helper components are applied to transform `head` and `body` elements. This means that tag helper component classes must check the `TagName` property of the output element to ensure they perform only their intended transformations. The tag helper component in Listing 25-30 looks for `body` elements and uses the `PreContent` property to insert a `div` element containing a timestamp before the rest of the element’s content.

> I show you how to increase the range of elements handled by tag helper components in the next section.

Tag helper components are registered as services that implement the `ITagHelperComponent` interface, as shown in Listing 25-31.

Listing 25-31. Registering a Tag Helper Component in the Program.cs File in the WebApp Folder
```cs
using Microsoft.EntityFrameworkCore;
using WebApp.Models;
using Microsoft.AspNetCore.Razor.TagHelpers;
using WebApp.TagHelpers;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddDbContext<DataContext>(opts => 
{
	opts.UseSqlServer(builder.Configuration["ConnectionStrings:ProductConnection"]);
	opts.EnableSensitiveDataLogging(true);
});

builder.Services.AddControllersWithViews();
builder.Services.AddRazorPages();
builder.Services.AddSingleton<CitiesData>();
// Registers tag helper componend as a service
builder.Services.AddTransient<ITagHelperComponent, TimeTagHelperComponent>();

var app = builder.Build();
app.UseStaticFiles();
app.MapControllers();
app.MapDefaultControllerRoute();
app.MapRazorPages();

var context = app.Services.CreateScope().ServiceProvider.GetRequiredService<DataContext>();
SeedData.SeedDatabase(context);

app.Run();
```

The `AddTransient` method is used to ensure that each request is handled using its own instance of the tag helper component class. To see the effect of the tag helper component, restart ASP.NET Core and use a browser to request http://localhost:5000/home. This response—and all other HTML responses from the application—contain the content generated by the tag helper component, as shown in Figure 25-14.

Figure 25-14. Using a tag helper component
![[zIMG-asp.net-tag-helpers-25-13.png]]

## Expanding Tag Helper Component Element Selection

By default, only the `head` and `body` elements are processed by the tag helper components, but additional elements can be selected by creating a class derived from the terribly named `TagHelperComponentTagHelper` class. Add a class file named *TableFooterTagHelperComponent.cs* to the *TagHelpers* folder and use it to define the classes shown in Listing 25-32.

Listing 25-32. The Contents of the TableFooterTagHelperComponent.cs File in the TagHelpers Folder
```cs
using Microsoft.AspNetCore.Mvc.Razor.TagHelpers;
using Microsoft.AspNetCore.Mvc.Rendering;
using Microsoft.AspNetCore.Razor.TagHelpers;

namespace WebApp.TagHelpers 
{
	[HtmlTargetElement("table")]
	public class TableFooterSelector: TagHelperComponentTagHelper 
	{
		public TableFooterSelector(ITagHelperComponentManager mgr, ILoggerFactory log): base(mgr, log) { }
	}
	
	public class TableFooterTagHelperComponent: TagHelperComponent 
	{
		public override void Process(TagHelperContext context, TagHelperOutput output) 
		{
			if (output.TagName == "table") 
			{
				TagBuilder cell = new TagBuilder("td");
				cell.Attributes.Add("colspan", "2");
				cell.Attributes.Add("class", "bg-dark text-white text-center");
				cell.InnerHtml.Append("Table Footer");
				
				TagBuilder row = new TagBuilder("tr");
				row.InnerHtml.AppendHtml(cell);
				
				TagBuilder footer = new TagBuilder("tfoot");
				footer.InnerHtml.AppendHtml(row);
				
				output.PostContent.AppendHtml(footer);
			}
		}
	}
}
```

The `TableFooterSelector` class is derived from `TagHelperComponentTagHelper`, and it is decorated with the `HtmlTargetElement` attribute that expands the range of elements processed by the application’s tag helper components. In this case, the attribute selects table elements.
The `TableFooterTagHelperComponent` class, defined in the same file, is a tag helper component that transforms `table` elements by adding a `tfoot` element, which represents a table footer.

> ![[zICO - Exclamation - 16.png]] Bear in mind that when you create a new `TagHelperComponentTagHelper`, all the tag helper components will receive the elements selected by the `HtmlTargetAttribute` element.

> ![[zICO - Warning - 16.png]] The tag helper component must be registered as a service to receive elements for transformation, but the tag helper component tag helper (which is one of the worst naming choices I have seen for some years) is discovered and applied automatically. 
 
Listing 25-33 adds the tag helper component service.

Listing 25-33. Registering a Tag Helper Component in the Program.cs File in the WebApp Folder
```cs
builder.Services.AddControllersWithViews();
builder.Services.AddRazorPages();
builder.Services.AddSingleton<CitiesData>();
// ---
builder.Services.AddTransient<ITagHelperComponent, TimeTagHelperComponent>();
builder.Services.AddTransient<ITagHelperComponent, TableFooterTagHelperComponent>();
```

Restart ASP.NET Core and use a browser to request a URL that renders a table, such as http://localhost:5000/home or http://localhost:5000/cities. Each table will contain a table footer, as shown in Figure 25-15.

Figure 25-15. Expanding tag helper component element selection
![[zIMG-asp.net-tag-helpers-25-15.png]]