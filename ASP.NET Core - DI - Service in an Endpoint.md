#ASP_NET_CORE/Platform/DI 

---

The situation is more complicated in the WeatherEndpoint class, which is static and doesn’t have a constructor through which dependencies can be declared. There are several approaches available to resolve dependencies for an endpoint class, which are described in the sections that follow.

## Getting Services from the HttpContext Object

Services can be accessed through the _HttpContext_ object that is received when a request is routed to an

```cs
using Platform.Services;

namespace Platform 
{
	public class WeatherEndpoint 
	{
		public static async Task Endpoint(HttpContext context) 
		{
			IResponseFormatter formatter = context.RequestServices.GetRequiredService<IResponseFormatter>();
			await formatter.Format(context, "Endpoint Class: It is cloudy in Milan");
		}
	}
}
```

The HttpContext.RequestServices property returns an object that implements the IServiceProvider interfaces, which provides access to the services that have been configured in the Program.cs file.

The Microsoft.Extensions.DependencyInjection namespace contains extension methods for the IServiceProvider interface that allow individual services to be obtained, as described in Table 14-3.

Name|Description
--|--
GetService&gt;T&lt;()|This method returns a service for the type specified by the generic type parameter or null if no such service has been defined.
GetService(type)|This method returns a service for the type specified or null if no such service has been defined.
GetRequiredService&gt;T&lt;()|This method returns a service specified by the generic type parameter and throws an exception if a service isn’t available.
GetRequiredService(type)|This method returns a service for the type specified and throws an exception if a service isn’t available.

## Using an Adapter Function

The drawback of using the HttpContext.RequestServices method is that the service must be resolved for every request that is routed to the endpoints. As you will learn later in the chapter, there are some services for which this is required because they provide features that are specific to a single request or response. This isn’t the case for the IResponseFormatter service, where a single object can be used to format multiple responses.

>A more elegant approach is to get the service when the endpoint’s route is created and not for each request.

```cs
using Platform.Services;
namespace Platform 
{
	public class WeatherEndpoint 
	{
		public static async Task Endpoint(HttpContext context, IResponseFormatter formatter) 
		{
			await formatter.Format(context, "Endpoint Class: It is cloudy in Milan");
		}
	}
}
```

```cs
using Platform.Services;
namespace Microsoft.AspNetCore.Builder 
{
	public static class EndpointExtensions 
	{
		public static void MapWeather(this IEndpointRouteBuilder app, string path) 
		{
			// Creates an adapter around the endpoit class
			IResponseFormatter formatter = app.ServiceProvider.GetRequiredService<IResponseFormatter>();
			app.MapGet(path, context => Platform.WeatherEndpoint.Endpoint(context, formatter));
		}
	}
}
```

```cs
using Platform;
using Platform.Services;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddSingleton<IResponseFormatter, HtmlResponseFormatter>();

var app = builder.Build();
app.UseMiddleware<WeatherMiddleware>();
app.MapGet("middleware/function", async (HttpContext context, IResponseFormatter formatter) => 
{
	await formatter.Format(context, "Middleware Function: It is snowing in Chicago");
});

app.MapWeather("endpoint/class");
app.MapGet("endpoint/function", async (HttpContext context, IResponseFormatter formatter) => 
{
	await formatter.Format(context, "Endpoint Function: It is sunny in LA");
});

app.Run();
```

The MapWeather extension method sets up the route and creates the adapter around the endpoint class.

## Using the _ActivatorUtilities_ Class

[ActivationUtilities](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities?view=dotnet-plat-ext-6.0)

I defined static methods for endpoint classes in Chapter 13 because it makes them easier to use when creating routes. But for endpoints that require services, it can often be easier to use a class that can be instantiated because it allows for a more generalized approach to handling services. Listing 14-19 revises the endpoint with a constructor and removes the static keyword from the Endpoint method.

```cs
using Platform.Services;

namespace Platform 
{
	public class WeatherEndpoint 
	{
		private IResponseFormatter formatter;
		
		public WeatherEndpoint(IResponseFormatter responseFormatter) 
		{
			formatter = responseFormatter;
		}
		
		public async Task Endpoint(HttpContext context) 
		{
			await formatter.Format(context, "Endpoint Class: It is cloudy in Milan");
		}
	}
}
```

The most common use of dependency injection in ASP.NET Core applications is in class constructors. Injection through methods, such as performed for middleware classes, is a complex process to re-create, but there are some useful built-in tools that take care of inspecting constructors and resolving dependencies using services, as shown in Listing 14-20.

```cs
using System.Reflection;

namespace Microsoft.AspNetCore.Builder 
{
	public static class EndpointExtensions 
	{
		public static void MapEndpoint<T>(this IEndpointRouteBuilder app, string path, string methodName = "Endpoint") 
		{
			MethodInfo? methodInfo = typeof(T).GetMethod(methodName);
			if (methodInfo == null || methodInfo.ReturnType != typeof(Task)) 
			{
				throw new System.Exception("Method cannot be used");
			}
			
			T endpointInstance = ActivatorUtilities.CreateInstance<T>(app.ServiceProvider);
			app.MapGet(path, (RequestDelegate)methodInfo.CreateDelegate(typeof(RequestDelegate), endpointInstance));
		}
	}
}
```

The extension method accepts a generic type parameter that specifies the endpoint class that will be used. The other arguments are the path that will be used to create the route and the name of the endpoint class method that processes requests.

The [ActivatorUtilities](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities?view=dotnet-plat-ext-6.0) class, defined in the Microsoft.Extensions.DependencyInjection namespace, provides methods for instantiating classes that have dependencies declared through their constructor.

Name|Description
--|--
CreateInstance&gt;T&lt;(services,args)|This method creates a new instance of the class specified by the type parameter, resolving dependencies using the services and additional (optional) arguments.
CreateInstance(services, type, args)|This method creates a new instance of the class specified by the parameter, resolving dependencies using the services and additional (optional) arguments.
GetServiceOrCreateInstance&gt;T&lt;(services, args)|This method returns a service of the specified type, if one is available, or creates a new instance if there is no service.
GetServiceOrCreateInstance(services, type, args)|This method returns a service of the specified type, if one is available, or creates a new instance if there is no service.

Both methods resolve constructor dependencies using services through an IServiceProvider object and an optional array of arguments that are used for dependencies that are not services. These methods make it easy to apply dependency injection to custom classes, and the use of the _CreateInstance_ method results in an extension method that can create routes with endpoint classes that consume services.

```cs
using Platform;
using Platform.Services;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddSingleton<IResponseFormatter, HtmlResponseFormatter>();

var app = builder.Build();
app.UseMiddleware<WeatherMiddleware>();
app.MapGet("middleware/function", async (HttpContext context, IResponseFormatter formatter) => 
{
	await formatter.Format(context, "Middleware Function: It is snowing in Chicago");
});

app.MapEndpoint<WeatherEndpoint>("endpoint/class");
app.MapGet("endpoint/function", async (HttpContext context, IResponseFormatter formatter) => 
{
	await formatter.Format(context, "Endpoint Function: It is sunny in LA");
});

app.Run();
```

This type of extension method makes it easy to work with endpoint classes and provides a similar experience to the UseMiddleware method.