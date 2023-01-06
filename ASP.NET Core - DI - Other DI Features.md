#aspnet_core/DI 

---

[[#Creating Dependency Chains]]

## Creating Dependency Chains

When a class is instantiated to resolve a service dependency, its constructor is inspected, and any dependencies on services are resolved. This allows one service to declare a dependency on another service, creating a chain that is resolved automatically. To demonstrate, add a class file called *TimeStamping.cs* to the *Platform/Services* folder with the code shown in Listing 14-32.

Listing 14-32. The Contents of the TimeStamping.cs File in the Services Folder
```cs
namespace Platform.Services 
{
	public interface ITimeStamper 
	{
		string TimeStamp { get; }
	}
	
	public class DefaultTimeStamper : ITimeStamper 
	{
		public string TimeStamp 
		{
			get => DateTime.Now.ToShortTimeString();
		}
	}
}
```

The class file defines an interface named `ITimeStamper` and an implementation class named `DefaultTimeStamper`. 
Next, add a file called *TimeResponseFormatter.cs* to the *Platform/Services* folder with the code shown in Listing 14-33.

Listing 14-33. The Contents of the TimeResponseFormatter.cs File in the Services Folder
```cs
namespace Platform.Services 
{
	public class TimeResponseFormatter : IResponseFormatter 
	{
		private ITimeStamper stamper;
		
		public TimeResponseFormatter(ITimeStamper timeStamper) 
		{
			stamper = timeStamper;
		}
		
		public async Task Format(HttpContext context, string content) 
		{
			await context.Response.WriteAsync($"{stamper.TimeStamp}: {content}");
		}
	}
}
```

The `TimeResponseFormatter` class is an implementation of the `IResponseFormatter` interface that declares a dependency on the `ITimeStamper` interface with a constructor parameter. 

> Services don’t need to have the same lifecycle as their dependencies, but you can end up with odd effects if you mix lifecycles. **Lifecycles are applied only when a dependency is resolved**, which means that if a
scoped service depends on a transient service, for example, then the transient object will behave as though it was assigned the scoped lifecycle.

Listing 14-34. Configuring Services in the Program.cs File in the Platform Folder
```cs
using Platform;
using Platform.Services;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddScoped<IResponseFormatter, TimeResponseFormatter>();
builder.Services.AddScoped<ITimeStamper, DefaultTimeStamper>();

var app = builder.Build();
app.UseMiddleware<WeatherMiddleware>();
app.MapGet("middleware/function", async (HttpContext context, IResponseFormatter formatter) => 
{
	await formatter.Format(context, "Middleware Function: It is snowing in Chicago");
});

app.MapEndpoint<WeatherEndpoint>("endpoint/class");
app.MapGet("endpoint/function", async (HttpContext context) => 
{
	IResponseFormatter formatter = context.RequestServices.GetRequiredService<IResponseFormatter>();
	await formatter.Format(context, "Endpoint Function: It is sunny in LA");
});

app.Run();
```

When a dependency on the IResponseFormatter service is resolved, the `TimeResponseFormatter` constructor will be inspected, and its dependency on the `ITimeStamper` service will be detected. A `DefaultTimeStamper` object will be created and injected into the `TimeResponseFormatter` constructor, which allows the original dependency to be resolved. 

## Accessing Services in the Program.cs File

A common requirement is to use the application’s configuration settings to alter the set of services that are created in the Program.cs file. This presents a problem because the configuration is presented as a service and services cannot normally be accessed until after the `WebApplicationBuilder.Build` method is invoked. To address this issue, the `WebApplication` and `WebApplicationBuilder` classes define properties that
provide access to the built-in services that provide access to the application configuration, as described in Table 14-6.

Name|Description
--|--
Configuration|This property returns an implementation of the IConfiguration interface, which provides access to the application’s configuration settings, as described in Chapter 15.
Environment|This property returns an implementation of the IWebHostEnvironment interface, which provides information about the environment in which the application is being executed
and whose principal use is to determine if the application is configured for development or deployment, as described in Chapter 15.

```cs
using Platform;
using Platform.Services;

var builder = WebApplication.CreateBuilder(args);
IWebHostEnvironment env = builder.Environment;

if (env.IsDevelopment()) 
{
	builder.Services.AddScoped<IResponseFormatter, TimeResponseFormatter>();
	builder.Services.AddScoped<ITimeStamper, DefaultTimeStamper>();
} 
else 
{
	builder.Services.AddScoped<IResponseFormatter, HtmlResponseFormatter>();
}

var app = builder.Build();

app.UseMiddleware<WeatherMiddleware>();
app.MapGet("middleware/function", async (HttpContext context, IResponseFormatter formatter) => 
{
	await formatter.Format(context, "Middleware Function: It is snowing in Chicago");
});

app.MapEndpoint<WeatherEndpoint>("endpoint/class");
app.MapGet("endpoint/function", async (HttpContext context) => 
{
	IResponseFormatter formatter = context.RequestServices.GetRequiredService<IResponseFormatter>();
	await formatter.Format(context, "Endpoint Function: It is sunny in LA");
});

app.Run();
```

This example uses the `Environment` property to get an implementation of the `IWebHostEnvironment` interface and uses its `IsDevelopment` method to decide which services are set up for the application.

## Using Service Factory Functions

Factory functions allow you to take control of the way that service implementation objects are created, rather than relying on ASP.NET Core to create instances for you. There are factory versions of the
`AddSingleton`, `AddTransient`, and `AddScoped` methods, all of which are used with a function that receives an `IServiceProvider` object and returns an implementation object for the service.
One use for factory functions is to define the implementation class for a service as a configuration setting, which is read through the `IConfguration` service. This requires the `WebApplicationBuilder`
properties described in the previous section.  Listing 14-36 adds a factory function for the `IResponseFormatter` service that gets the implementation class from the configuration data.

Listing 14-36. Using a Factory Function in the Program.cs File in the Platform Folder
```cs
using Platform;
using Platform.Services;

var builder = WebApplication.CreateBuilder(args);

IConfiguration config = builder.Configuration;
builder.Services.AddScoped<IResponseFormatter>(serviceProvider => 
{
	string? typeName = config["services:IResponseFormatter"];
	return (IResponseFormatter)ActivatorUtilities.CreateInstance(
		serviceProvider, 
		typeName == null
			? typeof(GuidService) 
			: Type.GetType(typeName, true)!);
});

builder.Services.AddScoped<ITimeStamper, DefaultTimeStamper>();

var app = builder.Build();
app.UseMiddleware<WeatherMiddleware>();
app.MapGet("middleware/function", async (HttpContext context, IResponseFormatter formatter) => 
{
	await formatter.Format(context, "Middleware Function: It is snowing in Chicago");
});

app.MapEndpoint<WeatherEndpoint>("endpoint/class");
app.MapGet("endpoint/function", async (HttpContext context) => 
{
	IResponseFormatter formatter = context.RequestServices.GetRequiredService<IResponseFormatter>();
	await formatter.Format(context, "Endpoint Function: It is sunny in LA");
});

app.Run();
```

The factory function reads a value from the configuration data, which is converted into a type and passed to the `ActivatorUtilities.CreateInstance` method. Listing 14-37 adds a configuration
setting to the *appsettings.Development.json* file that selects the `HtmlResponseFormatter` class as the implementation for the `IResponseFormatter` service. 

Listing 14-37. Defining a Setting in the appsettings.Development.json File in the Platform Folder
```json
{
	"Logging": {
		"LogLevel": {
			"Default": "Information",
			"Microsoft.AspNetCore": "Warning"
		}
	},
	"services": {
		"IResponseFormatter": "Platform.Services.HtmlResponseFormatter"
	}
}
```

When a dependency on the `IResponseFormatter` service is resolved, the factory function creates an instance of the type specified in the configuration file. 

## Creating Services with Multiple Implementations

Services can be defined with multiple implementations, which allows a consumer to select an implementation that best suits a specific problem. This is a feature that works best when the service interface
provides insight into the capabilities of each implementation class. To provide information about the capabilities of the `IResponseFormatter` implementation classes, add the default property shown in
Listing 14-38 to the interface.

Listing 14-38. Adding a Property in the IResponseFormatter.cs File in the Services Folder
```cs
namespace Platform.Services 
{
	public interface IResponseFormatter 
	{
		Task Format(HttpContext context, string content);
	
		public bool RichOutput => false;
	}
}
```

This `RichOutput` property will be false for implementation classes that don’t override the default value. To ensure there is one implementation that returns true, add the property shown in Listing 14-39 to the
`HtmlResponseFormatter` class.

Listing 14-39. Overriding a Property in the HtmlResponseFormatter.cs File in the Services Folder
```cs
namespace Platform.Services 
{
	public class HtmlResponseFormatter : IResponseFormatter 
	{
		public async Task Format(HttpContext context, string content) 
		{
			context.Response.ContentType = "text/html";
			await context.Response.WriteAsync($@"
				<!DOCTYPE html>
					<html lang=""en"">
						<head><title>Response</title></head>
							<body>
								<h2>Formatted Response</h2>
								<span>{content}</span>
							</body>
					</html>");
		}
		
		public bool RichOutput => true;
	}
}
```

Listing 14-40 registers multiple implementations for the IResponseFormatter service, which is done by making repeated calls to the `Add<lifecycle>` method. The listing also replaces the existing request pipeline
with two routes that demonstrate how the service can be used.

Listing 14-40. Defining and Using a Service in the Program.cs File in the Platform Folder
```cs
using Platform.Services;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddScoped<IResponseFormatter, TextResponseFormatter>();
builder.Services.AddScoped<IResponseFormatter, HtmlResponseFormatter>();
builder.Services.AddScoped<IResponseFormatter, GuidService>();

var app = builder.Build();
app.MapGet("single", async context => 
{
	IResponseFormatter formatter = context.RequestServices.GetRequiredService<IResponseFormatter>();
	await formatter.Format(context, "Single service");
});

app.MapGet("/", async context => 
{
	IResponseFormatter formatter = context.RequestServices.GetServices<IResponseFormatter>().First(f => f.RichOutput);
	await formatter.Format(context, "Multiple services");
});

app.Run();
```

The `AddScoped` statements register three services for the `IResponseFormatter` interface, each with a different implementation class. 

The route for the */single* URL uses the `IServiceProvider.GetRequiredService<T>` method to request a service, like this:
```cs
context.RequestServices.GetRequiredService<IResponseFormatter>();
```
This is a service consumer that is unaware that there are multiple implementations available. **The service is resolved using the most recently registered implementation**, which is the `GuidService` class.

The other endpoint is a service consumer that is aware that multiple implementations may be available and that requests the service using the `IServiceProvider.GetServices<T>`` method.
```cs
context.RequestServices.GetServices<IResponseFormatter>().First(f => f.RichOutput);
```
This method returns an `IEnumerable<IResponseFormatter>` that enumerates the available implementations. These are filtered using the LINQ `First` method to select an implementation whose
`RichOutput` property returns `true`. 

## Using Unbound Types in Services

Services can be defined with generic type parameters that are bound to specific types when the service is requested, as shown in Listing 14-41.

Listing 14-41. Using an Unbound Type in the Program.cs File in the Platform Folder
```cs
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddSingleton(typeof(ICollection<>), typeof(List<>));

var app = builder.Build();
app.MapGet("string", async context => 
{
	ICollection<string> collection = context.RequestServices.GetRequiredService<ICollection<string>>();
	collection.Add($"Request: { DateTime.Now.ToLongTimeString() }");
	
	foreach (string str in collection) 
	{
		await context.Response.WriteAsync($"String: {str}\n");
	}
});

app.MapGet("int", async context => 
{
	ICollection<int> collection = context.RequestServices.GetRequiredService<ICollection<int>>();
	collection.Add(collection.Count() + 1);
	foreach (int val in collection) 
	{
		await context.Response.WriteAsync($"Int: {val}\n");
	}
});

app.Run();
```

This feature relies on the versions of the `AddSIngleton`, `AddScoped`, and `AddTransient` methods that accept types as conventional arguments and cannot be performed using generic type arguments. The
service in Listing 14-41 is created with unbound types, like this:
```cs
services.AddSingleton(typeof(ICollection<>), typeof(List<>));
```
When a dependency on an `ICollection<T>` service is resolved, a `List<T>` object will be created so that a dependency on `ICollection<string>`, for example, will be resolved using a `List<string>` object. Rather
than require separate services for each type, the unbound service allows mappings for all generic types to be created. The two endpoints in Listing 14-41 request `ICollection<string>` and `ICollection<int>` services, each of which will be resolved with a different `List<T>` object.