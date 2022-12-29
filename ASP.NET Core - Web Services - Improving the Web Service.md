#ASP_NET_CORE/WebServices 

---

The controller in Listing 19-13 re-creates the functionality provided by the separate endpoints, but there are still improvements that can be made, as described in the following sections.

## Supporting cross-origin requests (CORS)

If you are supporting third-party JavaScript clients, you may need to enable support for cross-origin requests (CORS). Browsers protect users by only allowing JavaScript code to make HTTP requests within the same origin, which means to URLs that have the same scheme, host, and port as the URL used to load the JavaScript code. CORS loosens this restriction by performing an initial HTTP request to check that the server will allow requests originating from a specific URL, helping prevent malicious code using your service without the user’s consent.

ASP.NET Core provides a built-in service that handles CORS, which is enabled by adding the following statement to the Program.cs file:
```cs
builder.Services.AddCors();
```

The options pattern is used to configure CORS with the `CorsOptions` class defined in the `Microsoft.AspNetCore.Cors.Infrastructure` namespace. See [https://docs.microsoft.com/en-gb/aspnet/core/security/cors?view=aspnetcore-3.1](https://docs.microsoft.com/en-gb/aspnet/core/security/cors?view=aspnetcore-3.1) for details.

## Using Asynchronous Actions

The ASP.NET Core platform processes each request by assigning a thread from a pool. **The number of requests that can be processed concurrently is limited to the size of the pool,** and a thread can’t be used to process any other request while it is waiting for an action to produce a result. Actions that depend on external resources can cause a request thread to wait for an extended period. A database server, for example, may have its own concurrency limits and may queue up queries until they can be executed. The ASP.NET Core request thread is unavailable to process any other requests until the database produces a result for the action, which then produces a response that can be sent to the HTTP client.

This problem can be addressed by defining asynchronous actions, which allow ASP.NET Core threads to process other requests when they would otherwise be blocked, increasing the number of HTTP requests that the application can process simultaneously. Listing 19-14 revises the controller to use asynchronous actions.

> ![[zICO - Warning - 16.png]] Asynchronous actions don’t produce responses any quicker, and the benefit is only to increase the number of requests that can be processed concurrently.

Listing 19-14. Asynchronous Actions in the ProductsController.cs File in the Controllers Folder
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
		public IAsyncEnumerable<Product> GetProducts() 
		{
			return context.Products.AsAsyncEnumerable();
		}
		
		[HttpGet("{id}")]
		public async Task<Product?> GetProduct(long id) 
		{
			return await context.Products.FindAsync(id);
		}
		
		[HttpPost]
		public async Task SaveProduct([FromBody] Product product) 
		{
			await context.Products.AddAsync(product);
			await context.SaveChangesAsync();
		}
	
		[HttpPut]
		public async Task UpdateProduct([FromBody] Product product) 
		{
			context.Update(product);
			await context.SaveChangesAsync();
		}
		
		[HttpDelete("{id}")]
		public async Task DeleteProduct(long id) 
		{
			context.Products.Remove(new Product() { ProductId = id });
			await context.SaveChangesAsync();
		}
	}
}
```

Entity Framework Core provides asynchronous versions of some methods, such as `FindAsync`, `AddAsync`, and `SaveChangesAsync`, and I have used these with the `await` keyword. Not all operations can be performed asynchronously, which is why the `Update` and `Remove` methods are unchanged within the `UpdateProduct` and `DeleteProduct` actions.

> ![[zICO - Warning - 16.png]] For some operations—including LINQ queries to the database—the `IAsyncEnumerable<T>` interface can be used, which denotes a sequence of objects that should be enumerated asynchronously and prevents the ASP.NET Core request thread from waiting for each object to be produced by the database, as explained in Chapter 5.
> There is no change to the responses produced by the controller, but the threads that ASP.NET Core assigns to process each request are not necessarily blocked by the action methods.

## Preventing Over-Binding

Some of the action methods use the model binding feature to get data from the response body so that it can be used to perform database operations. There is a problem with the `SaveProduct` action, which can be seen by using a PowerShell prompt to run the command shown in Listing 19-15.

Listing 19-15. Saving a Product
`Invoke-RestMethod http://localhost:5000/api/products -Method POST -Body (@{ProductId=100; Name="Swim Buoy"; Price=19.99; CategoryId=1; SupplierId=1} | ConvertTo-Json) -ContentType "application/json"`

Unlike the command that was used to test the `POST` method, this command includes a value for the `ProductId` property. When Entity Framework Core sends the data to the database, the following exception is thrown:
`Microsoft.Data.SqlClient.SqlException (0x80131904): Cannot insert explicit value for identity column in table 'Products' when IDENTITY_INSERT is set to OFF.`

By default, Entity Framework Core configures the database to assign primary key values when new objects are stored. This means the application doesn’t have to worry about keeping track of which key values have already been assigned and allows multiple applications to share the same database without the need to coordinate key allocation. The `Product` data model class needs a `ProductId` property, but the model binding process doesn’t understand the significance of the property and adds any values that the client provides to the objects it creates, which causes the exception in the `SaveProduct` action method.

This is known as over-binding, and it can cause serious problems when a client provides values that the developer didn’t expect. At best, the application will behave unexpectedly, but this technique has been used to subvert application security and grant users more access than they should have.

The safest way to prevent over-binding is to create separate data model classes that are used only for receiving data through the model binding process. Add a class file named *ProductBindingTarget.cs* to the *WebApp/Models* folder and use it to define the class shown in Listing 19-16.

Listing 19-16. The Contents of the ProductBindingTarget.cs File in the WebApp/Models Folder
```cs
namespace WebApp.Models 
{
	public class ProductBindingTarget 
	{
		public string Name { get; set; } = "";
		public decimal Price { get; set; }
		public long CategoryId { get; set; }
		public long SupplierId { get; set; }
		
		public Product ToProduct() => new Product() 
		{
			Name = this.Name, 
			Price = this.Price,
			CategoryId = this.CategoryId, 
			SupplierId = this.SupplierId
		};
	}
}
```

The `ProductBindingTarget` class defines only the properties that the application wants to receive from the client when storing a new object. The `ToProduct` method creates a `Product` that can be used with the rest of the application, ensuring that the client can provide properties only for the `Name`, `Price`, `CategoryId`, and `SupplierId` properties. Listing 19-17 uses the binding target class in the SaveProduct action to prevent over-binding.

Listing 19-17. Using a Binding Target in the ProductsController.cs File in the Controllers Folder
```cs
[HttpPost]
public async Task SaveProduct([FromBody] ProductBindingTarget target) 
{
	await context.Products.AddAsync(target.ToProduct());
	await context.SaveChangesAsync();
}
```

The client has included the `ProductId` value, but it is ignored by the model binding process, which discards values for read-only properties. (You may see a different value for the `ProductId` property when you run this example depending on the changes you made to the database before running the command.)

## Using Action Results

ASP.NET Core sets the status code for responses automatically, but you won’t always get the result you desire, in part because there are no firm rules for RESTful web services, and the assumptions that Microsoft makes may not match your expectations. To see an example, use a PowerShell command prompt to run the command shown in Listing 19-18, which sends a `GET` request to the web service.

Listing 19-18. Sending a GET Request
`Invoke-WebRequest http://localhost:5000/api/products/1000 | Select-Object StatusCode`

The Invoke-WebRequest command is similar to the Invoke-RestMethod command used in earlier examples but makes it easier to get the status code from the response. The URL requested in Listing 19-18 will be handled by the GetProduct action method, which will query the database for an object whose `ProductId` value is *1000*, and the command produces the following output:
```txt
StatusCode
----------
204
```

There is no matching object in the database, which means that the `GetProduct` action method returns `null`. When the MVC Framework receives `null` from an action method, it returns the `204` status code, which indicates a successful request that has produced no data. Not all web services behave this way, and a common alternative is to return a `404` response, indicating not found. Similarly, the `SaveProducts` action will return a `200` response when it stores an object, but since the primary key isn’t generated until the data is stored, the client doesn’t know what key value was assigned.

> There is no right or wrong when it comes to these kinds of web service implementation details, and you should pick the approaches that best suit your project and personal preferences. This section is an example of how to change the default behavior and not a direction to follow any specific style of web service.

Action methods can direct the MVC Framework to send a specific response by returning an object that implements the `IActionResult` interface, which is known as an **action result**. This allows the action method to specify the type of response that is required without having to produce it directly using the `HttpResponse` object.

The `ControllerBase` class provides a set of methods that are used to create action result objects, which can be returned from action methods. Table 19-7 describes the most useful action result methods.

Table 19-7. Useful ControllerBase Action Result Methods
Name|Description
--|--
Ok|The IActionResult returned by this method produces a `200 OK` status code and sends an optional data object in the response body.
NoContent|The `IActionResult` returned by this method produces a `204 NO CONTENT` status code.
BadRequest|The `IActionResult` returned by this method produces a `400 BAD REQUEST` status code. The method accepts an optional model state object that describes the problem to the client, as demonstrated in the “Validating Data” section.
File|The `IActionResult` returned by this method produces a `200 OK` response, sets the `Content-Type` header to the specified type, and sends the specified file to the client.
NotFound|The `IActionResult` returned by this method produces a `404 NOT FOUND` status code.
Redirect, RedirectPermanent|The `IActionResult` returned by these methods redirects the client to a specified URL.
RedirectToRoute, RedirectToRoutePermanent|The `IActionResult` returned by these methods redirects the client to the specified URL that is created using the routing system, using convention routing, as described in the “Redirecting Using Route Values” sidebar.
LocalRedirect, LocalRedirectPermanent|The `IActionResult` returned by these methods redirects the client to the specified URL that is local to the application.
RedirectToAction, RedirectToActionPermanent|The `IActionResult` returned by these methods redirects the client to an action method. The URL for the redirection is created using the URL routing system.
RedirectToPage, RedirectToPagePermanent|The `IActionResult` returned by these methods redirects the client to a Razor Page, described in Chapter 23.
StatusCode|The `IActionResult` returned by this method produces a response with a specific status code.

When an action method returns an object, it is equivalent to passing the object to the `Ok` method and returning the result. 
When an action returns `null`, it is equivalent to returning the result from the `NoContent` method. 

Listing 19-19 revises the behavior of the `GetProduct` and `SaveProduct` actions so they use the methods from Table 19-7 to override the default behavior for web service controllers.

Listing 19-19. Using Action Results in the ProductsController.cs File in the Controllers Folder
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
		public IAsyncEnumerable<Product> GetProducts() 
		{
			return context.Products.AsAsyncEnumerable();
		}
		
		[HttpGet("{id}")]
		public async Task<IActionResult> GetProduct(long id) 
		{
			Product? p = await context.Products.FindAsync(id);
			if (p == null) 
			{
				return NotFound();
			}
			return Ok(p);
		}
		
		[HttpPost]
		public async Task<IActionResult> SaveProduct([FromBody] ProductBindingTarget target) 
		{
			Product p = target.ToProduct();
			await context.Products.AddAsync(p);
			await context.SaveChangesAsync();
			return Ok(p);
		}
		
		[HttpPut]
		public async Task UpdateProduct([FromBody] Product product) 
		{
			context.Update(product);
			await context.SaveChangesAsync();
		}
		
		[HttpDelete("{id}")]
		public async Task DeleteProduct(long id) 
		{
			context.Products.Remove(new Product() { ProductId = id });
			await context.SaveChangesAsync();
		}
	}
}
```

Restart ASP.NET Core and repeat the command from Listing 19-18, and you will see an exception, which is how the `Invoke-WebRequest` command responds to error status codes, such as the `404 Not Found` returned by the `GetProduct` action method.
To see the effect of the change to the SaveProduct action method, use a PowerShell command prompt to run the command shown in Listing 19-20, which sends a `POST` request to the web service.

Listing 19-20. Sending a POST Request
`Invoke-RestMethod http://localhost:5000/api/products -Method POST -Body (@{Name="Boot Laces"; Price=19.99; CategoryId=2; SupplierId=2} | ConvertTo-Json) -ContentType "application/json"``

The command will produce the following output, showing the values that were parsed from the JSON data received from the web service:
```txt
productId : 13
name : Boot Laces
price : 19.99
categoryId : 2
category :
supplierId : 2
supplier :
Performing Redirections
```

Many of the action result methods in Table 19-7 relate to redirections, which redirect the client to another URL. The most basic way to perform a redirection is to call the `Redirect` method, as shown in Listing 19-21.

> The `LocalRedirect` and `LocalRedirectPermanent` methods throw an exception if a controller tries to perform a redirection to any URL that is not local. This is useful when you are redirecting to URLs provided by users, where an open redirection attack is attempted to redirect another user to an untrusted site.

Listing 19-21. Redirecting in the ProductsController.cs File in the Controllers Folder
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
		
		// ...other action methods omitted for brevity...
		
		[HttpGet("redirect")]
		public IActionResult Redirect() 
		{
			return Redirect("/api/products/1");
		}
	}
}
```

The redirection URL is expressed as a string argument to the `Redirect` method, which produces a temporary redirection. Restart ASP.NET Core and use a PowerShell command prompt to run the command shown in Listing 19-22, which sends a `GET` request that will be handled by the new action method.

Listing 19-22. Testing Redirection
`Invoke-RestMethod http://localhost:5000/api/products/redirect`

The `Invoke-RestMethod` command will receive the redirection response from the web service and send a new request to the URL it is given, producing the following response:
```txt
productId : 1
name : GreenKayak
price : 275.00
categoryId : 1
category :
supplierId : 1
supplier :
```

### Redirecting to an Action Method

You can redirect to another action method using the `RedirectToAction` method (for temporary redirections) or the `RedirectToActionPermanent` method (for permanent redirections). Listing 19-23 changes the `Redirect` action method so that the client will be redirected to another action method defined by the controller.

Listing 19-23. Redirecting to an Action the ProductsController.cs File in the Controllers Folder
```cs
[HttpGet("redirect")]
public IActionResult Redirect() 
{
	return RedirectToAction(nameof(GetProduct), new { Id = 1 });
}
```

The action method is specified as a string, although the `nameof` expression can be used to select an action method without the risk of a typo. Any additional values required to create the route are supplied using an anonymous object. Restart ASP.NET Core and use a PowerShell command prompt to repeat the command in Listing 19-22. 

The routing system will be used to create a URL that targets the specified action method, producing the following response:
```txt
productId : 1
name : GreenKayak
price : 275.00
categoryId : 1
category :
supplierId : 1
supplier :
```

If you specify only an action method name, then the redirection will target the current controller. There is an overload of the `RedirectToAction` method that accepts action and controller names.

### Redirecting using route values

The `RedirectToRoute` and `RedirectToRoutePermanent` methods redirect the client to a URL that is created by providing the routing system with values for segment variables and allowing it to select a route to use. This can be useful for applications with complex routing configurations, and caution should be used because it is easy to create a redirection to the wrong URL. Here is an example of redirection with the `RedirectToRoute` method:
```cs
[HttpGet("redirect")]
public IActionResult Redirect() 
{
	return RedirectToRoute(new { controller = "Products", action = "GetProduct", Id = 1 });
}
```

The set of values in this redirection relies on convention routing to select the controller and action method. Convention routing is typically used with controllers that produce HTML responses, as described in Chapter 21.
