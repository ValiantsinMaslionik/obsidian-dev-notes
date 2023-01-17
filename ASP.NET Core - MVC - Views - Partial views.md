#aspnet_core/mvc/view

---

You will often need to use the same set of HTML elements and expressions in several different places. *Partial views* are views that contain fragments of content that will be included in other views to produce complex responses without duplication.

# Enabling Partial Views

Partial views are applied using a feature called *tag helpers*, which are described in detail in Chapter 25; tag helpers are configured in the view imports file, which was added to the project in Chapter 21. To enable the feature required for partial views, add the statement shown in Listing 22-25 to the \_ViewImports.cshtml file.

Listing 22-25. Enabling Tag Helpers in the \_ViewImports.cshtml File in the Views Folder
```html
@using WebApp.Models
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```

# Creating a Partial View

Partial views are just regular CSHTML files, and it is only the way they are used that differentiates them from standard views. If you are using Visual Studio, right-click the Views/Home folder, select Add ➤ New Item, and use the Razor View template to create a file named \_RowPartial.cshtml. Once the file has been created, replace the contents with those shown in Listing 22-26. If you are using Visual Studio Code, add a file named \_RowPartial.cshtml to the *Views/Home* folder and add to it the content shown in Listing 22-26.

> Visual Studio provides some tooling support for creating prepopulated partial views, but the simplest way to create a partial view is to create a regular view using the Razor View item template.

Listing 22-26. The Contents of the \_RowPartial.cshtml File in the Views/Home Folder
```html
@model Product
<tr>
	<td>@Model?.Name</td>
	<td>@Model?.Price</td>
</tr>
```

The model expression is used to define the view model type for the partial view, which contains the same mix of expressions and HTML elements as regular views. The content of this partial view creates a table row, using the `Name` and `Price` properties of a `Product` object to populate the table cells.

# Applying a Partial View

Partial views are applied by adding a `partial` element in another view or layout. In Listing 22-27, I have added the element to the List.cshtml file so the partial view is used to generate the rows in the table.

Listing 22-27. Using a Partial View in the List.cshtml File in the Views/Home Folder
```html
@model IEnumerable<Product>
@{
	Layout = null;
	decimal average = Model?.Average(p => p.Price) ?? 0;
}
<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<h6 class="bg-secondary text-white text-center m-2 p-2">Products</h6>
	<div class="m-2">
		<table class="table table-sm table-striped table-bordered">
			<thead>
				<tr><th>Name</th><th>Price</th></tr>
			</thead>
			<tbody>
				@foreach (Product p in Model ?? Enumerable.Empty<Product>()) 
				{
					<partial name="_RowPartial" model="p" />
				}
			</tbody>
		</table>
	</div>
</body>
</html>
```

The attributes applied to the partial element control the selection and configuration of the partial view, as described in Table 22-2.

Table 22-2. The partial Element Attributes
Name|Description
--|--
name|This property specifies the name of the partial view, which is located using the same search process as regular views.
model|This property specifies the value that will be used as the view model object for the partial view.
for|This property is used to define an expression that selects the view model object for the partial view, as explained in [[#Selecting the Partial View Model Using an Expression]].
view-data|This property is used to provide the partial view with additional data.

The partial element in Listing 22-27 uses the `name` attribute to select the \_RowPartial view and the `model` attribute to select the `Product` object that will be used as the view model object. The `partial` element is applied within the `@foreach` expression, which means that it will be used to generate each row in the table.

## Selecting the Partial View Model Using an Expression

The `for` attribute is used to set the partial view’s model using an expression that is applied to the view’s model, which is a feature more easily demonstrated than described. Add a partial view named \_CellPartial.cshtml to the *Views/Home* folder with the content shown in Listing 22-28.

Listing 22-28. The Contents of the \_CellPartial.cshtml File in the Views/Home Folder
```html
@model string
<td class="bg-info text-white">@Model</td>
```

This partial view has a `string` view model object, which it uses as the contents of a table cell element; the table cell element is styled using the Bootstrap CSS framework. In Listing 22-29, I have added a `partial` element to the \_RowPartial.cshtml file that uses the \_CellPartial partial view to display the table cell for the name of the `Product` object.

Listing 22-29. Using a Partial View in the \_RowPartial.cshtml File in the Views/Home Folder
```html
@model Product
<tr>
	<partial name="_CellPartial" for="Name" />
	<td>@Model?.Price</td>
</tr>
```

The `for` attribute selects the `Name` property as the model for the \_CellPartial partial view. To see the effect, use a browser to request http://localhost:5000/home/list, which will produce the response shown in Figure 22-15.

Figure 22-15. Selecting a model property for use in a partial view
![[zIMG-asp.net-mvc-views-22-15.png]]

## ![[zICO - Warning - 16.png]] USING TEMPLATED DELEGATES

Templated delegates are an alternative way of avoiding duplication in a view. Templated delegates are defined in a code block, like this:
```html
@{
	Func<Product, object> row = @<tr><td>@item.Name</td><td>@item.Price</td></tr>;
}
```

The *template* is a function that accepts a `Product` input object and returns a dynamic result. Within the template expression, the input object is referred to as item in expressions. The templated delegate is invoked as a method expression to generate content.
```html
<tbody>
	@foreach (Product p in Model) 
	{
		@row(p)
	}
</tbody>
```

> I find this feature awkward and prefer using partial views, although this is a matter of preference and habit rather than any objective problems with the way that templated delegates work.
