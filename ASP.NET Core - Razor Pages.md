#aspnet_core/razor_pages

---

In this chapter, I introduce how to use *Razor Pages*, which is a simpler approach to generating HTML content, intended to capture some of the enthusiasm for the legacy ASP.NET Web Pages framework. I explain how Razor Pages work, explain how they differ from the controllers and views approach taken by the MVC Framework, and show you how they fit into the wider ASP.NET Core platform. The process of explaining how Razor Pages work can minimize the differences from the controllers and views described in earlier chapters. You might form the impression that Razor Pages are just MVC-lite and dismiss them, which would be a shame. Razor Pages are interesting because of the developer experience and not the way they are implemented.
My advice is to give Razor Pages a chance, especially if you are an experienced MVC developer. Although the technology used will be familiar, the process of creating application features is different and is well-suited to small and tightly focused features that don’t require the scale and complexity of controllers and views. I have been using the MVC Framework since it was first introduced, and I admit to ignoring the early releases of Razor Pages. Now, however, I find myself mixing Razor Pages and the MVC Framework in most projects.

Question|Answer
--|--
What are they?|Razor Pages are a simplified way of generating HTML responses.
Why are they useful?|The simplicity of Razor Pages means you can start getting results sooner than with the MVC Framework, which can require a relatively complex preparation process. Razor Pages are also easier for less experienced web developers to understand because the relationship between the code and content is more obvious.
How are they used?|Razor Pages associate a single view with the class that provides it with features and use a file-based routing system to match URLs.
Are there any pitfalls or limitations?|Razor Pages are less flexible than the MVC Framework, which makes them unsuitable for complex applications. Razor Pages can be used only to generate HTML responses and cannot be used to create RESTful web services.
Are there any alternatives?|The MVC Framework’s approach of controllers and views can be used instead of Razor Pages.

# Preparing for This Chapter

This chapter uses the WebApp project from Chapter 22. Open a new PowerShell command prompt, navigate to the folder that contains the WebApp.csproj file, and run the command shown in Listing 23-1 to drop the database.

Listing 23-1. Dropping the Database
`dotnet ef database drop --force`

## Running the Example Application

Once the database has been dropped, use the PowerShell command prompt to run the command shown in Listing 23-2.

Listing 23-2. Running the Example Application
`dotnet run`

The database will be seeded as part of the application startup.

The `dotnet watch` command can be useful with Razor Pages development, but it doesn’t handle the initial configuration of the services and middleware or changes to the routing configuration, which is why I have returned to the `dotnet run` command in this chapter.

# Understanding Razor Pages

As you learn how Razor Pages work, you will see they share functionality with the MVC Framework. In fact, Razor Pages are typically described as a simplification of the MVC Framework—which is true—but that doesn’t give any sense of why Razor Pages can be useful.
The MVC Framework solves every problem in the same way: a controller defines action methods that select views to produce responses. It is a solution that works because it is so flexible: the controller can define multiple action methods that respond to different requests, the action method can decide which view will be used as the request is being processed, and the view can depend on private or shared partial views to produce its response.
Not every feature in web applications needs the flexibility of the MVC Framework. For many features, a single action method will be used to handle a wide range of requests, all of which are dealt with using the same view. Razor Pages offer a more focused approach that ties together markup and C# code, sacrificing flexibility for focus.
But Razor Pages have limitations. Razor Pages tend to start out focusing on a single feature but slowly grow out of control as enhancements are made. **And, unlike MVC controllers, Razor Pages cannot be used to create web services**.
You don’t have to choose just one model because the MVC Framework and Razor Pages coexist, as demonstrated in this chapter. This means that self-contained features can be easily developed with Razor Pages, leaving the more complex aspects of an application to be implemented using the MVC controllers and actions.

# Configuring Razor Pages

To prepare the application for Razor Pages, statements must be added to the Program.cs file to set up services and configure the endpoint routing system, as shown in Listing 23-3.

Listing 23-3. Configuring the Application in the Program.cs File in the WebApp Folder
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
builder.Services.AddDistributedMemoryCache();
builder.Services.AddSession(options => 
{
	options.Cookie.IsEssential = true;
});

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

The `AddRazorPages` method sets up the service that is required to use Razor Pages, and the `MapRazorPages` method creates the routing configuration that matches URLs to pages, which is explained later in the chapter.

# Creating a Razor Page

Razor Pages are defined in the *Pages* folder. If you are using Visual Studio, create the *WebApp/Pages* folder, right-click it in the Solution Explorer, select Add ➤ New Item from the pop-up menu, and select the *Razor Page* template, as shown in Figure 23-2. Set the Name field to *Index.cshtml* and click the *Add* button to create the file.

If you are using Visual Studio Code, create the WebApp/Pages folder and add to it a new file named Index.cshtml with the content shown in Listing 23-4.

Listing 23-4. The Contents of the Index.cshtml File in the Pages Folder
```html
@page
@model IndexModel
@using Microsoft.AspNetCore.Mvc.RazorPages
@using WebApp.Models;

<!DOCTYPE html>
<html>
<head>
	<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>
	<div class="bg-primary text-white text-center m-2 p-2">@Model.Product?.Name</div>
</body>
</html>

@functions 
{
	public class IndexModel: PageModel 
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

Razor Pages use the Razor syntax that I described in Chapters 21 and 22, and Razor Pages even use the same CSHTML file extension. But there are some important differences.

The `@page` directive must be the first thing in a Razor Page, which ensures that the file is not mistaken for a view associated with a controller. But the most important difference is that the `@functions` directive is used to define the C# code that supports the Razor content in the same file. 

# Understanding the Page Model

In a Razor Page, the `@model` directive is used to select a page model class, rather than identifying the type of the object provided by an action method. The ``@model` directive in Listing 23-4 selects the IndexModel class.
```html
@model IndexModel
```

The page model is defined within the `@functions` directive and is derived from the PageModel class, like this:
```html
@functions 
{
	public class IndexModel: PageModel 
	{
```

When the Razor Page is selected to handle an HTTP request, a new instance of the page model class is created, and dependency injection is used to resolve any dependencies that have been declared using constructor parameters, using the features described in Chapter 14. The `IndexModel` class declares a dependency on the `DataContext` service created in Chapter 18, which allows it to access the data in the database.
After the page model object has been created, a handler method is invoked. The name of the handler method is *On*, followed by the HTTP method for the request so that the `OnGet` method is invoked when the Razor Page is selected to handle an HTTP `GET` request. Handler methods can be asynchronous, in which case a GET request will invoke the `OnGetAsync` method, which is the method implemented by the `IndexModel` class.
Values for the handler method parameters are obtained from the HTTP request using the model binding process, which is described in detail in Chapter 28. The `OnGetAsync` method receives the value for its `id` parameters from the model binder, which it uses to query the database and assign the result to its `Product` property.

# Understanding the Page View

Razor Pages use the same mix of HTML fragments and code expressions to generate content, which defines the view presented to the user. The page model’s methods and properties are accessible in the Razor Page through the `@Model` expression. The `Product` property defined by the `IndexModel` class is used to set the content of an HTML element, like this:
```html
<div class="bg-primary text-white text-center m-2 p-2">@Model.Product?.Name</div>
```

The `@Model` expression returns an `IndexModel` object, and this expression reads the `Name` property of the object returned by the `Product` property.

> The null conditional operator (`?`) isn’t required for the `Model` property because it will always be assigned an instance of the page model class and cannot be null. 

The properties defined by the page model class can be null, however, which is why I have used the operator for the `Product` property in the Razor expression:
```html
<div class="bg-primary text-white text-center m-2 p-2">@Model.Product?.Name</div>
```

# Understanding the Generated C# Class

Behind the scenes, Razor Pages are transformed into C# classes, just like regular Razor views. Here is a simplified version of the C# class that is produced from the Razor Page in Listing 23-4:
```cs
namespace AspNetCoreGeneratedDocument 
{
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Threading.Tasks;
	using Microsoft.AspNetCore.Mvc;
	using Microsoft.AspNetCore.Mvc.Rendering;
	using Microsoft.AspNetCore.Mvc.ViewFeatures;
	using Microsoft.AspNetCore.Mvc.RazorPages;
	using WebApp.Models;

	internal sealed class Pages_Index : Microsoft.AspNetCore.Mvc.RazorPages.Page 
	{
		public async override global::System.Threading.Tasks.Task ExecuteAsync() 
		{
			WriteLiteral("\r\n<!DOCTYPE html>\r\n<html>\r\n");
			__tagHelperExecutionContext = __tagHelperScopeManager.Begin("head",	TagMode.StartTagAndEndTag, "7d534...", async() => 
			{
				WriteLiteral("\r\n<link href=\"/lib/bootstrap/css/bootstrap.min.css\"rel=\"stylesheet\" />\r\n");
			});
			HeadTagHelper = CreateTagHelper<TagHelpers.HeadTagHelper>();
			__tagHelperExecutionContext.Add(HeadTagHelper);
			Write(__tagHelperExecutionContext.Output);
			WriteLiteral("\r\n");
			__tagHelperExecutionContext = __tagHelperScopeManager.Begin("body", TagMode.StartTagAndEndTag, "7d534...", async() => 
			{
				WriteLiteral("\r\n<div class=\"bg-primary text-white text-center m-2 p-2\">");
				Write(Model.Product?.Name);
				WriteLiteral("</div>\r\n");
			});
			BodyTagHelper = CreateTagHelper<TagHelpers.BodyTagHelper>();
			__tagHelperExecutionContext.Add(BodyTagHelper);
			Write(__tagHelperExecutionContext.Output);
			WriteLiteral("\r\n</html>\r\n\r\n");
		}
		
		public class IndexModel: PageModel 
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

		public IModelExpressionProvider ModelExpressionProvider { get; private set; }
		public IUrlHelper Url { get; private set; }
		public IViewComponentHelper Component { get; private set; }
		public IJsonHelper Json { get; private set; }
		public IHtmlHelper<IndexModel> Html { get; private set; }
		public ViewDataDictionary<IndexModel> ViewData => (ViewDataDictionary<IndexModel>)PageContext?.ViewData;
		public IndexModel Model => ViewData.Model;
	}
}
```

If you compare this code with the equivalent shown in Chapter 21, you can see how Razor Pages rely on the same features used by the MVC Framework. The HTML fragments and view expressions are transformed into calls to the `WriteLiteral` and `Write` methods.

> The process for seeing the generated C# classes for Razor Pages is the same as for regular Razor Views, as described in Chapter 21.

