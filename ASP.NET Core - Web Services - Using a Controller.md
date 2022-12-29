#ASP_NET_CORE/WebServices

---

The drawback of using individual endpoints to create a web service is that each endpoint has to duplicate a similar set of steps to produce a response: get the Entity Framework Core service so that it can query the database, set the `Content-Type` header for the response, serialize the objects into JSON, and so on. As a result, web services created with endpoints are difficult to understand and awkward to maintain, and the Program.cs file quickly becomes unwieldy.

A more elegant and robust approach is to use a controller, which allows a web service to be defined in a single class. Controllers are part of the MVC Framework, which builds on the ASP.NET Core platform and takes care of handling data in the same way that endpoints take care of processing URLs.

> **THE RISE AND FALL OF THE MVC PATTERN IN ASP.NET CORE**
>The MVC Framework is an implementation of the **Model-View-Controller** pattern, which describes one way to structure an application. The examples in this chapter use two of the three pillars of the pattern: a data model (the M in MVC) and controllers (the C in MVC). Chapter 21 provides the missing piece and explains how views can be used to create HTML responses using Razor. The MVC pattern was an important step in the evolution of ASP.NET and allowed the platform to break away from the **Web Forms** model that predated it. Web Forms applications were easy to start but quickly became difficult to manage and hid details of HTTP requests and responses from the developer. By contrast, the adherence to the MVC pattern provided a strong and scalable structure for applications written with the MVC Framework and hid nothing from the developer. The MVC Framework revitalized ASP.NET and provided the foundation for what became ASP.NET Core, which dropped support for Web Forms and focused solely on using the MVC pattern. 

>As ASP.NET Core evolved, other styles of web application have been embraced, and the MVC Framework is only one of the ways that applications can be created. That doesn’t undermine the utility of the MVC pattern, but it doesn’t have the central role that it used to in ASP.NET Core development, and the features that used to be unique to the MVC Framework can now be accessed through other approaches, such as **Razor Pages** and **Blazor**.

> A consequence of this evolution is that understanding the MVC pattern is no longer a prerequisite for effective ASP.NET Core development. If you are interested in understanding the MVC pattern, then [https://en.wikipedia.org/wiki/Model–view–controller](https://en.wikipedia.org/wiki/Model–view–controller) is a good place to start. But for this book, understanding how the features provided by the MVC Framework build on the ASP.NET Core platform is all the context that is required.

## Enabling the MVC Framework

The first step to creating a web service using a controller is to configure the MVC framework, which requires a service and an endpoint, as shown in Listing 19-5. This listing also removes the endpoints defined in the previous section.

Listing 19-5. Enabling the MVC Framework in the Program.cs File in the WebApp Folder
```cs
using Microsoft.EntityFrameworkCore;
using WebApp.Models;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddDbContext<DataContext>(opts => 
{
	opts.UseSqlServer(builder.Configuration["ConnectionStrings:ProductConnection"]);
	opts.EnableSensitiveDataLogging(true);
});

builder.Services.AddControllers();

var app = builder.Build();
app.MapControllers();
app.MapGet("/", () => "Hello World!");

var context = app.Services.CreateScope().ServiceProvider.GetRequiredService<DataContext>();
SeedData.SeedDatabase(context);

app.Run();
```

The `AddControllers` method defines the services that are required by the MVC framework, and the `MapControllers` method defines routes that will allow controllers to handle requests. You will see other methods used to configure the MVC framework used in later chapters, which provide access to different features, but the methods used in Listing 19-5 are the ones that configure the MVC framework for web services.

## Creating a Controller

Controllers are classes whose methods, known as *actions*, can process HTTP requests. Controllers are discovered automatically when the application is started. The basic discovery process is simple: any public class whose name ends with *Controller* is a controller, and any public method a controller defines is an *action*. To demonstrate how simple a controller can be, create the WebApp/Controllers folder and add to it a file named ProductsController.cs with the code shown in Listing 19-6.

> Controllers are conventionally defined in the *Controllers* folder, but they can be defined anywhere in the project and will still be discovered.

Listing 19-6. The Contents of the ProductsController.cs File in the Controllers Folder
```cs
using Microsoft.AspNetCore.Mvc;
using WebApp.Models;

namespace WebApp.Controllers 
{
	[Route("api/[controller]")]
	public class ProductsController : ControllerBase 
	{
		[HttpGet]
		public IEnumerable<Product> GetProducts() 
		{
			return new Product[] 
			{
				new Product() { Name = "Product #1" },
				new Product() { Name = "Product #2" }
			};
		}
		
		[HttpGet("{id}")]
		public Product GetProduct() 
		{
			return new Product() 
			{
				ProductId = 1, Name = "Test Product"
			};
		}
	}
}
```

The `ProductsController` class meets the criteria that the MVC framework looks for in a controller. It defines public methods named `GetProducts` and `GetProduct`, which will be treated as actions.

## Understanding the Base Class

Controllers are derived from the `ControllerBase` class, which provides access to features provided by the MVC Framework and the underlying ASP.NET Core platform. Table 19-4 describes the most useful properties provided by the `ControllerBase` class.

> Although controllers are typically derived from the `ControllerBase` or `Controller` classes (described in Chapter 21), this is just convention, and the MVC Framework will accept any class whose name ends with *Controller*, that is derived from a class whose name ends with *Controller*, or that has been decorated with the `Controller` attribute. Apply the `NonController` attribute to classes that meet these criteria but that should not receive HTTP requests.

Table 19-4. Useful ControllerBase Properties
Name|Description
--|--
HttpContext|This property returns the HttpContext object for the current request.
ModelState|This property returns details of the data validation process, as demonstrated in the “Validating Data” section later in the chapter and described in detail in Chapter 29.
Request|This property returns the HttpRequest object for the current request.
Response|This property returns the HttpResponse object for the current response.
RouteData|This property returns the data extracted from the request URL by the routing middleware, as described in Chapter 13.
User|This property returns an object that describes the user associated with the current request, as described in Chapter 38.

A new instance of the controller class is created each time one of its actions is used to handle a request, which means the properties in Table 19-4 describe only the current request.

## Understanding the Controller Attributes

The HTTP methods and URLs supported by the action methods are determined by the combination of attributes that are applied to the controller. 
The URL for the controller is specified by the Route attribute, which is applied to the class, like this:
```cs
[Route("api/[controller]")]
public class ProductsController: ControllerBase {
```

> The `[controller]` part of the attribute argument is used to derive the URL from the name of the controller class. The *Controller* part of the class name is dropped, which means that the attribute in Listing 19-6 sets the URL for the controller to */api/products*.

Each action is decorated with an attribute that specifies the HTTP method that it supports, like this:
```cs
[HttpGet]
public Product[] GetProducts() {
```

> The name given to action methods doesn’t matter in controllers used for web services. There are other uses for controllers, described in Chapter 21, where the name does matter, but for web services, it is the HTTP method attributes and the route patterns that are important.

The `HttpGet` attribute tells the MVC framework that the `GetProducts` action method will handle HTTP `GET` requests. Table 19-5 describes the full set of attributes that can be applied to actions to specify HTTP methods.

Table 19-5. The HTTP Method Attributes
Name|Description
--|--
HttpGet|This attribute specifies that the action can be invoked only by HTTP requests that use the `GET` verb.
HttpPost|This attribute specifies that the action can be invoked only by HTTP requests that use the `POST` verb.
HttpDelete|This attribute specifies that the action can be invoked only by HTTP requests that use the `DELETE` verb.
HttpPut|This attribute specifies that the action can be invoked only by HTTP requests that use the `PUT` verb.
HttpPatch|This attribute specifies that the action can be invoked only by HTTP requests that use the `PATCH` verb.
HttpHead|This attribute specifies that the action can be invoked only by HTTP requests that use the `HEAD` verb.
AcceptVerbs|This attribute is used to specify multiple HTTP verbs.

The attributes applied to actions to specify HTTP methods can also be used to build on the controller’s base URL.
```cs
[HttpGet("{id}")]
public Product GetProduct() {
```

This attribute tells the MVC framework that the `GetProduct` action method handles `GET` requests for the URL pattern *api/products/{id}*. During the discovery process, the attributes applied to the controller are used to build the set of URL patterns that the controller can handle, summarized in Table 19-6.

> ![[zICO - Warning - 16.png]] When writing a controller, it is important to ensure that each combination of the HTTP method and URL pattern that the controller supports is mapped to only one action method. An exception will be thrown when a request can be handled by multiple actions because the MVC Framework is unable to decide which to use.

Table 19-6. The URL Patterns
HTTP Method|URL Pattern|Action Method Name
--|--|--
GET|api/products|GetProducts
GET|api/products/{id}|GetProduct

You can see how the combination of attributes is equivalent to the `MapGet` methods I used for the same URL patterns when I used endpoints to create a web service earlier in the chapter.

## GET AND POST: PICK THE RIGHT ONE

The rule of thumb is that `GET` requests should be used for all read-only information retrieval, while `POST` requests should be used for any operation that changes the application state. In standard's compliance terms, `GET` requests are for safe interactions (having no side effects besides information retrieval), and `POST` requests are for unsafe interactions (making a decision or changing something).
These conventions are set by the World Wide Web Consortium (W3C), at [www.w3.org/Protocols/rfc2616/rfc2616-sec9.html](www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

`GET` requests are addressable: all the information is contained in the URL, so it’s possible to bookmark and link to these addresses. 

>![[zICO - Exclamation - 16.png]] **Do not use `GET` requests for operations that change state!** 
>Many web developers learned this the hard way in 2005 when Google Web Accelerator was released to the public. This application prefetched all the content linked from each page, which is legal within the HTTP because `GET` requests should be safe. Unfortunately, many web developers had ignored the HTTP conventions and placed simple links to “delete item” or “add to shopping cart” in their applications. Chaos ensued.

## Understanding Action Method Results

One of the main benefits provided by controllers is that the MVC Framework takes care of setting the response headers and serializing the data objects that are sent to the client. You can see this in the results defined by the action methods, like this:
```cs
[HttpGet("{id}")]
public Product GetProduct() {
```

When I used an endpoint, I had to work directly with the JSON serializer to create a string that can be written to the response and set the `Content-Type` header to tell the client that the response contained JSON data. The action method returns a `Product` object, which is processed automatically.

## Using Dependency Injection in Controllers

A new instance of the controller class is created each time one of its actions is used to handle a request. The application’s services are used to resolve any dependencies the controller declares through its constructor and any dependencies that the action method defines. This allows services that are required by all actions to be handled through the constructor while still allowing individual actions to declare their own dependencies, as shown in Listing 19-7.

Listing 19-7. Using Services in the ProductsController.cs File in the Controllers Folder
```cs
using Microsoft.AspNetCore.Mvc;
using WebApp.Models;

namespace WebApp.Controllers 
{
	[Route("api/[controller]")]
	public class ProductsController : ControllerBase 
	{
		private DataContext context;
		
		public ProductsController(DataContext ctx) 
		{
			context = ctx;
		}
		
		[HttpGet]
		public IEnumerable<Product> GetProducts() 
		{
			return context.Products;
		}
		
		[HttpGet("{id}")]
		public Product? GetProduct([FromServices]ILogger<ProductsController> logger) 
		{
			logger.LogDebug("GetProduct Action Invoked");
			return context.Products.FirstOrDefault();
		}
	}
}
```

The constructor declares a dependency on the `DataContext` service, which provides access to the application’s data. **The services are resolved using the request scope**, which means that a controller can request all services, without needing to understand their lifecycle.

> **THE ENTITY FRAMEWORK CORE CONTEXT SERVICE LIFECYCLE**
> A new Entity Framework Core context object is created for each controller. Some developers will try to reuse context objects as a perceived performance improvement, but this causes problems because data from one query can affect subsequent queries. Behind the scenes, Entity Framework Core efficiently manages the connections to the database, and you should not try to store or reuse context objects outside of the controller for which they are created.

The `GetProducts` action method uses the DataContext to request all the `Product` objects in the database. The `GetProduct` method also uses the `DataContext` service, but it declares a dependency on `ILogger<T>`, which is the logging service described in Chapter 15. 

Dependencies that are declared by action methods must be decorated with the `FromServices` attribute, like this:
```cs
public Product GetProduct([FromServices] ILogger<ProductsController> logger) {
```

By default, the MVC Framework attempts to find values for action method parameters from the request URL, and the `FromServices` attribute overrides this behavior. 

>![[zICO - Warning - 16.png]] One consequence of the controller lifecycle is that you can’t rely on side effects caused by methods being called in a specific sequence. So, for example, I can’t assign the `ILogger<T>` object received by the `GetProduct` method in Listing 19-7 to a property that can be read by the `GetProducts` action in later requests. **Each controller object is used to handle one request**, and only one action method will be invoked by the MVC Framework for each object.

## Using Model Binding to Access Route Data

In the previous section, I noted that the MVC Framework uses the request URL to find values for action method parameters, a process known as model binding. Model binding is described in detail in Chapter 28, but Listing 19-8 shows a simple example.

Listing 19-8. Using Model Binding in the ProductsController.cs File in the Controllers Folder
```cs
using Microsoft.AspNetCore.Mvc;
using WebApp.Models;

namespace WebApp.Controllers 
{
	[Route("api/[controller]")]
	public class ProductsController : ControllerBase 
	{
		private DataContext context;
		
		public ProductsController(DataContext ctx) 
		{
			context = ctx;
		}
		
		[HttpGet]
		public IEnumerable<Product> GetProducts() 
		{
			return context.Products;
		}
		
		[HttpGet("{id}")]
		public Product? GetProduct(long id, [FromServices] ILogger<ProductsController> logger) 
		{
			logger.LogDebug("GetProduct Action Invoked");
			return context.Products.Find(id);
		}
	}
}
```

The listing adds a long parameter named `id` to the `GetProduct` method. When the action method is invoked, the MVC Framework injects the value with the same name from the routing data, automatically converting it to a long value, which is used by the action to query the database using the LINQ Find method.

## Model Binding from the Request Body

The model binding feature can also be used on the data in the request body, which allows clients to send data that is easily received by an action method. Listing 19-9 adds a new action method that responds to `POST` requests and allows clients to provide a JSON representation of the Product object in the request body.

Listing 19-9. Adding an Action in the ProductsController.cs File in the Controllers Folder
```cs
using Microsoft.AspNetCore.Mvc;
using WebApp.Models;

namespace WebApp.Controllers 
{
	[Route("api/[controller]")]
	public class ProductsController : ControllerBase 
	{
		private DataContext context;
		
		public ProductsController(DataContext ctx) 
		{
			context = ctx;
		}
		
		[HttpGet]
		public IEnumerable<Product> GetProducts() 
		{
			return context.Products;
		}
		
		[HttpGet("{id}")]
		public Product? GetProduct(long id, [FromServices] ILogger<ProductsController> logger) 
		{
			logger.LogDebug("GetProduct Action Invoked");
			return context.Products.Find(id);
		}
		
		[HttpPost]
		public void SaveProduct([FromBody] Product product) 
		{
			context.Products.Add(product);
			context.SaveChanges();
		}
	}
}
```

The new action relies on two attributes. The `HttpPost` attribute is applied to the action method and tells the MVC Framework that the action can process POST requests. The `FromBody` attribute is applied to the action’s parameter, and it specifies that the value for this parameter should be obtained by parsing the request body. When the action method is invoked, the MVC Framework will create a new `Product` object and populate its properties with the values in the request body. The model binding process can be complex and is usually combined with data validation, as described in Chapter 29.

## Adding Additional Actions

Now that the basic features are in place, I can add actions that allow clients to replace and delete `Product` objects using the HTTP `PUT` and `DELETE` methods, as shown in Listing 19-11.

Listing 19-11. Adding Actions in the ProductsController.cs File in the Controllers Folder
```cs
using Microsoft.AspNetCore.Mvc;
using WebApp.Models;

namespace WebApp.Controllers 
{
	[Route("api/[controller]")]
	public class ProductsController : ControllerBase 
	{
		private DataContext context;
	
		public ProductsController(DataContext ctx) 
		{
			context = ctx;
		}

		[HttpGet]
		public IEnumerable<Product> GetProducts() 
		{
			return context.Products;
		}
		
		[HttpGet("{id}")]
		public Product? GetProduct(long id,	[FromServices] ILogger<ProductsController> logger) 
		{
			logger.LogDebug("GetProduct Action Invoked");
			return context.Products.Find(id);
		}
		
		[HttpPost]
		public void SaveProduct([FromBody] Product product) 
		{
			context.Products.Add(product);
			context.SaveChanges();
		}
		
		[HttpPut]
		public void UpdateProduct([FromBody] Product product) 
		{
			context.Products.Update(product);
			context.SaveChanges();
		}
		
		[HttpDelete("{id}")]
		public void DeleteProduct(long id) 
		{
			context.Products.Remove(new Product() { ProductId = id });
			context.SaveChanges();
		}
	}
}
```

The `UpdateProduct` action is similar to the `SaveProduct` action and uses model binding to receive a `Product` object from the request body. 
The `DeleteProduct` action receives a primary key value from the URL and uses it to create a Product that has a value only for the `ProductId` property, which is required because Entity Framework Core works only with objects, but web service clients typically expect to be able to delete objects using just a key value.

Listing 19-12. Updating an Object
`Invoke-RestMethod http://localhost:5000/api/products -Method PUT -Body (@{ ProductId=1;Name="Green Kayak"; Price=275; CategoryId=1; SupplierId=1} | ConvertTo-Json) -ContentType "application/json"`
Listing 19-13. Deleting an Object
`Invoke-RestMethod http://localhost:5000/api/products/2 -Method DELETE`
