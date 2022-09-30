#ASP_NET_CORE/Platform/DI 

---

Table 14-5. The Extension Methods for Creating Services

Name|Description
--|--
AddSingleton&gt;T, U&lt;()|This method creates a single object of type U that is used to resolve all dependencies on type T.
AddTransient&gt;T, U&lt;()|This method creates a new object of type U to resolve each dependency on type T.
AddScoped&gt;T, U&lt;()|This method creates a new object of type U that is used to resolve dependencies on T within a single scope, such as request.

## Creating Transient Services

 The AddTransient method does the opposite of the AddSingleton method and creates a new instance of the implementation class for every dependency that is resolved. To create a service that will demonstrate the use of service 
 lifecycles, add a file called GuidService.cs to the Platform/Services folder with the code shown

```cs
namespace Platform.Services 
{
	public class GuidService : IResponseFormatter 
	{
		private Guid guid = Guid.NewGuid();
		public async Task Format(HttpContext context, string content) 
		{
			await context.Response.WriteAsync($"Guid: {guid}\n{content}");
		}
	}
}
```

The Guid struct generates a unique identifier, which will make it obvious when a different instance
is used to resolve a dependency on the IResponseFormatter interface. 

>![[zICO - Warning - 16.png]] The following snippet is not correct! See [[#Avoiding the Transient Service Reuse Pitfall]] section.

```cs
using Platform;
using Platform.Services;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddTransient<IResponseFormatter, GuidService>();

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

Each response will be shown with a different GUID value, confirming that transient service objects have been used to resolve the dependencies on the IResponseFormatter service for the endpoint and the middleware component.

### Avoiding the Transient Service Reuse Pitfall

The previous example demonstrated that when different service objects are created, the effect is not quite as you might expect, which you can see by clicking the reload buttons. Rather than seeing new GUID values, responses contain the same value. 

>![[zICO - Exclamation - 16.png]] New service objects are created only when dependencies are resolved, not when services are used. 

The components and endpoints in the example application ==have their dependencies resolved only when the application starts and the top-level statements in the Program.cs file are executed==. Each receives a separate service object, which is then reused for every request that is processed. To solve this problem ==for the middleware component, the dependency for the service can be moved to the Invoke method==

```cs
using Platform.Services;

namespace Platform 
{
	public class WeatherMiddleware 
	{
		private RequestDelegate next;
		
		public WeatherMiddleware(RequestDelegate nextDelegate, IResponseFormatter respFormatter) 
		{
			next = nextDelegate;
		}
		
		public async Task Invoke(HttpContext context, IResponseFormatter formatter) 
		{
			if (context.Request.Path == "/middleware/class") 
			{
				await formatter.Format(context, "Middleware Class: It is raining in London");
			}
			else 
			{
				await next(context);
			}
		}
	}
}
```

The ASP.NET Core platform will resolve dependencies declared by the Invoke method every time a request is processed, which ensures that a new transient service object is created.

>![[zICO - Warning - 16.png]] The [[ASP.NET Core - DI - Using a Service in an Endpoint#Using the _ActivatorUtilities_ Class|ActivatorUtilities]] class doesn’t deal with resolving dependencies for methods, and ASP.NET Core includes this feature only for middleware components. 

The simplest way of solving this issue for endpoints is to ==explicitly request services when each request is handled==, which is the approach I used earlier when showing how services are used. It is also possible to enhance the extension method to request services on behalf of an endpoint.

Listing 14-25 ^db94ef

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
			ParameterInfo[] methodParams = methodInfo!.GetParameters();
			app.MapGet(path, context => (Task)(methodInfo.Invoke(endpointInstance, methodParams.Select(p => p.ParameterType == typeof(HttpContext) 
				? context
				: app.ServiceProvider.GetService(p.ParameterType)).ToArray()))!);
		}
	}
}
```

The code in [[#^db94ef|Listing 14-25]] isn’t as efficient as the approach taken by the ASP.NET Core platform for middleware components. All the parameters defined by the method that handles requests are treated as services to be resolved, except for the HttpContext parameter. A route is created with a delegate that resolves the services for every request and invokes the method that handles the request. 

[[#^a46f17|Listing 14-26]] revises the WeatherEndpoint class to move the dependency on IResponseFormatter to the Endpoint method so that a new service object will be received for every request.

Listing 14-26 ^a46f17

```cs
using Platform.Services;

namespace Platform 
{
	public class WeatherEndpoint 
	{
		public async Task Endpoint(HttpContext context, IResponseFormatter formatter) 
		{
			await formatter.Format(context, "Endpoint Class: It is cloudy in Milan");
		}
	}
}
```

The changes in Listing 14-24 to Listing 14-26 ensure that the transient service is resolved for every request, which means that a new GuidService object is created and every response contains a unique ID.


## Using Scoped Services

Scoped services strike a balance between singleton and transient services. Within a scope, dependencies are resolved with the same object. A new scope is started for each HTTP request, which means that a service object will be shared by all the components that handle that request. To prepare for a scoped service, Listing 14-27 changes the WeatherMiddleware class to declare three dependencies on the same service.

```cs
using Platform.Services;
namespace Platform 
{
	public class WeatherMiddleware 
	{
		private RequestDelegate next;
		public WeatherMiddleware(RequestDelegate nextDelegate) 
		{
			next = nextDelegate;
		}
		public async Task Invoke(HttpContext context, IResponseFormatter formatter1, IResponseFormatter formatter2, IResponseFormatter formatter3) 
		{
			if (context.Request.Path == "/middleware/class") 
			{
				await formatter1.Format(context, string.Empty);
				await formatter2.Format(context, string.Empty);
				await formatter3.Format(context, string.Empty);
			}
			else
			{
				await next(context);
			}
		}
	}
}
```

> Since the IResponseFormatter service was created with the AddTransient method, each dependency is resolved with a different object. 

 >![[zICO - Warning - 16.png]] You can create scopes through the [_CreateScope()_](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.createscope?view=dotnet-plat-ext-6.0)** extension method for the IServiceProvider interface. The result is an IServiceProvider that is associated with a new scope and that will have its own implementation objects for scoped services.

```cs
using Platform;
using Platform.Services;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddScoped<IResponseFormatter, GuidService>();

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

Restart ASP.NET Core and you will see that the same GUID is used to resolve all three dependencies declared by the middleware component. Core creates a new scope and a new service object.

### Avoiding the Scoped Service Validation Pitfall

Service consumers are unaware of the lifecycle that has been selected for singleton and transient services: they declare a dependency or request a service and get the object they require. ==Scoped services can be used only within a scope.== A new scope is created automatically for each request that was received. 

>![[zICO - Exclamation - 16.png]] Requesting a scoped service outside of a scope causes an exception. 

The extension method that configures the endpoint resolves services through an IServiceProvider object obtained from the routing middleware, like this:
```cs
...
app.MapGet(path, context => (Task)(methodInfo.Invoke(endpointInstance, methodParams.Select(p => p.ParameterType == typeof(HttpContext)
	? context 
	: app.ServiceProvider.GetService(p.ParameterType)).ToArray()))!);

app.MapGet(path, context => (Task)methodInfo.Invoke(endpointInstance, methodParams.Select(p => p.ParameterType == typeof(HttpContext)
	? context 
	: app.ServiceProvider.GetService(p.ParameterType)).ToArray()));
...
```

### Accessing Scoped Services Through the Context Object

The [[ASP.NET Core - HttpContext|HttpContext]] class defines a *RequestServices* property that returns an *IServiceProvider* object that allows access to scoped services, as well as singleton and transient services. This fits well with the most common use of scoped services, which is to use a single service object for each HTTP request. 

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
			ParameterInfo[] methodParams = methodInfo!.GetParameters();
			
			app.MapGet(path, context => 
				(Task)(methodInfo.Invoke(endpointInstance, methodParams.Select(p => p.ParameterType == typeof(HttpContext)
					? context
					: context.RequestServices.GetService(p.ParameterType)).ToArray()))!);
		}
	}
}
```

Only dependencies that are declared by the method that handles the request are resolved using the HttpContext.RequestServices property. Services that are declared by an endpoint class constructor are still resolved using the 
EndpointRouteBuilder.ServiceProvider property, which ensures that endpoints don’t use scoped services inappropriately.

### Creating New Handlers for Each Request

The problem with the extension method is that it requires endpoint classes to know the lifecycles for the services they depend on. The WeatherEndpoint class depends on the IResponseFormatter service and must know that a dependency can be declared only through the Endpoint method and not the constructor.
To remove the need for this knowledge, a new instance of the endpoint class can be created to handle each request, as shown in Listing 14-30, which allows constructor and method dependencies to be resolved without needing to know which services are scoped.

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
			ParameterInfo[] methodParams = methodInfo!.GetParameters();
			
			app.MapGet(path, context => 
			{
				T endpointInstance = ActivatorUtilities.CreateInstance<T>(context.RequestServices);
				return (Task)methodInfo.Invoke(endpointInstance!, methodParams.Select(p => p.ParameterType == typeof(HttpContext)
					? context
					: context.RequestServices.GetService(p.ParameterType)).ToArray())!;
			});
		}
	}
}
```

This approach requires a new instance of the endpoint class to handle each request, but it ensures that no knowledge of service lifecycles is required.

### Using Scoped Services in Lambda Expressions

The HttpContext class can also be used in middleware components and endpoints that are defined as lambda expressions, as shown in Listing 14-31.

```cs
using Platform;
using Platform.Services;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddScoped<IResponseFormatter, GuidService>();

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

### Accesing scoped services when configuring the request pipeline

If you require a scoped service to configure the application in the Program.cs file, then you can create a new scope and then request the service, like this:

```cs
...
app.Services.CreateScope().ServiceProvider.GetRequiredService<MyScopedService>();
...
```

> The _CreateScope_ method creates a scope that allows scoped services to be accessed. If you try to obtain a scoped service without creating a scope, then you will receive an exception.
