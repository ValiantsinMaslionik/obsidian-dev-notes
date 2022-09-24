#ASP_NET_CORE/Platform 

---

[[#Creating middleware using Use method]]
[[#Creating middleware using a class]]
[[#Understanding the Return Pipeline Path]]
[[#Short-Circuiting the Request Pipeline]]
[[#Creating Pipeline Branches]]
[[#Branching with a predicate]]
[[#Creating Terminal Middleware]]

## Creating middleware using Use method

The key method for creating middleware is _Use_
```
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.Use(async (context, next) => 
{
	if (context.Request.Method == HttpMethods.Get && context.Request.Query["custom"] == "true") 
	{
		context.Response.ContentType = "text/plain";
		await context.Response.WriteAsync("Custom Middleware \n");
	}
	await next();
});
app.MapGet("/", () => "Hello World!");
app.Run();
```
The _Use_ method registers a middleware component that is typically expressed as a lambda function
that receives each request as it passes through the pipeline (there is another method used for classes, as
described in the next section).

The arguments to the lambda function are an [[ASP.NET Core - HttpContext|HttpContext]] object and a function that is invoked to tell
ASP.NET Core to pass the request to the next middleware component in the pipeline.

When creating custom middleware, the HttpContext, [[ASP.NET Core - HttpRequest|HttpRequest]], and [[ASP.NET Core - HttpResponse|HttpRequest]] objects are used
directly, but, as you will learn in later chapters, this isn’t usually required when using the higher-level ASP.
NET Core features such as the MVC Framework and Razor Pages.

==Setting the Content-Type header is important because it prevents the subsequent middleware
component from trying to set the response status code and headers.== ASP.NET Core will always try to make
sure that a valid HTTP response is sent, and this can lead to the response headers or status code being set
after an earlier component has already written content to the response body, which produces an exception
(because the headers have to be sent to the client before the response body can begin).

The second argument to the middleware is the function conventionally named *next* that tells ASP.NET
Core to pass the request to the next component in the request pipeline.
No arguments are required when invoking the next middleware component because ASP.NET Core
takes care of providing the component with the HttpContext object and its own next function so that it can
process the request. The next function is asynchronous, which is why the await keyword is used and why
the lambda function is defined with the async keyword.

## Creating middleware using a class

Defining middleware using lambda functions is convenient, but it can lead to a long and complex series
of statements in the Program.cs file and makes it hard to reuse middleware in different projects.
Middleware can also be defined using classes.
```
namespace Platform 
{
	public class QueryStringMiddleWare 
	{
		private RequestDelegate next;
		public QueryStringMiddleWare(RequestDelegate nextDelegate) 
		{
			next = nextDelegate;
		}
		public async Task Invoke(HttpContext context) 
		{
			if (context.Request.Method == HttpMethods.Get && context.Request.Query["custom"] == "true") 
			{
				if (!context.Response.HasStarted) 
				{
					context.Response.ContentType = "text/plain";
				}
				await context.Response.WriteAsync("Class-based Middleware \n");
			}
			await next(context);
		}
	}
}
```
Middleware classes receive a _RequestDelegate_ as a constructor parameter, which is used to forward the
request to the next component in the pipeline. The _Invoke_ method is called by ASP.NET Core when a request
is received and receives an HttpContext object that provides access to the request and response, using the
same classes that lambda function middleware receives. The RequestDelegate returns a Task, which allows
it to work asynchronously.

==One important difference in class-based middleware is that the HttpContext object must be used as an
argument when invoking the RequestDelegate to forward the request.==

Class-based middleware components are added to the pipeline with the UseMiddleware method, which
accepts the middleware as a type argument
```
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.Use(async (context, next) => 
{
	if (context.Request.Method == HttpMethods.Get && context.Request.Query["custom"] == "true") 
	{
		context.Response.ContentType = "text/plain";
		await context.Response.WriteAsync("Custom Middleware \n");
	}
	await next();
});
app.UseMiddleware<Platform.QueryStringMiddleWare>();
app.MapGet("/", () => "Hello World!");
app.Run();
```
When the ASP.NET Core is started, the QueryStringMiddleware class will be instantiated, and its Invoke
method will be called to process requests as they are received.

>**Caution** A single middleware object is used to handle all requests, which means that the code in the nvoke method must be thread-safe

## Understanding the Return Pipeline Path

```
app.Use(async (context, next) => 
{
	await next();
	await context.Response.WriteAsync($"\nStatus Code: { context.Response.StatusCode}");
});
```
The new middleware immediately calls the next method to pass the request along the pipeline and
then uses the WriteAsync method to add a string to the response body. This may seem like an odd approach,
but it allows middleware to make changes to the response before and after it is passed along the request
pipeline by defining statements before and after the next function is invoked.

![[zIMG-ASP.NET-return-pipeline-path.png]]

Middleware can operate before the request is passed on, after the request has been processed by other
components, or both. The result is that several middleware components collectively contribute to the
response that is produced, each providing some aspect of the response or providing some feature or data
that is used later in the pipeline.
>**Note** Middleware components must not change the response status code or headers once ASP.NET Core
>has started to send the response to the client. Check the [[ASP.NET Core - HttpResponse#^c49c34|HasStarted]] property to avoid exceptions.

## Short-Circuiting the Request Pipeline

Components that generate complete responses can choose not to call the next function so that the request
isn’t passed on. Components that don’t pass on requests are said to short-circuit the pipeline, which is what
the new middleware component shown in Listing 12-10 does for requests that target the /short URL.
```
app.Use(async (context, next) => 
{
	if (context.Request.Path == "/short") 
	{
		await context.Response.WriteAsync($"Request Short Circuited");
	} 
	else 
	{
		await next();
	}
});
```

![[zIMG-ASP.NET-short-circuiting-pipeline.png]]

## Creating Pipeline Branches

The Map method is used to create a section of pipeline that is used to process requests for specific URLs,
creating a separate sequence of middleware components.
```
// The use of global imports, described in Chapter 5, requires disambiguation between extension methods
// named Map defined for different interfaces implemented by the WebApplication class. In this case, it is the
// Map extension method for the IApplicationBuilder interface that is required, and a cast is required to
// ensure this is the method that is invoked
((IApplicationBuilder)app).Map("/branch", branch => 
{
	branch.UseMiddleware<Platform.QueryStringMiddleWare>();
	branch.Use(async (HttpContext context, Func<Task> next) => 
	{
		await context.Response.WriteAsync($"Branch Middleware");
	});
});
```

![[zIMG-ASP.NET-pipeline-branching.png]]

## Branching with a predicate

ASP.NET Core also supports the _MapWhen_ method, which can match requests using a predicate,
allowing requests to be selected for a pipeline branch on criteria other than just URLs.

The arguments to the MapWhen method are a predicate function that receives an [[ASP.NET Core - HttpContext|HttpContext]]
and that returns true for requests that should follow the branch, and a function that receives an
_IApplicationBuilder_ object representing the pipeline branch, to which middleware is added. Here
is an example of using the MapWhen method to branch the pipeline:
```
app.MapWhen(
	context => context.Request.Query.Keys.Contains("branch"), 
	branch => 
	{
		// ...add middleware components here...
	}
);
```
The predicate function returns true to branch for requests whose query string contains a parameter
named branch. A cast to the IApplicationBuilder interface is not required because there only one
MapWhen extension method has been defined.

## Creating Terminal Middleware

Terminal middleware never forwards requests to other components and always marks the end of the request
pipeline. There is a terminal middleware component in the Program.cs file, as shown here:
```
branch.Use(async (context, next) => 
{
	await context.Response.WriteAsync($"Branch Middleware");
});
```
ASP.NET Core supports the _Run_ method as a convenience feature for creating terminal middleware,
which makes it obvious that a middleware component won’t forward requests and that a deliberate decision
has been made not to call the next function. In Listing 12-12, I have used the Run method for the terminal
middleware in the pipeline branch.
```
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
((IApplicationBuilder)app).Map("/branch", branch => 
{
	branch.UseMiddleware<Platform.QueryStringMiddleWare>();
	branch.Run(async (context) => 
	{
		await context.Response.WriteAsync($"Branch Middleware");
	});
});
app.UseMiddleware<Platform.QueryStringMiddleWare>();
app.MapGet("/", () => "Hello World!");
app.Run();
```
The middleware function passed to the Run method receives only an [[ASP.NET Core - HttpContext|HttpContext]] object and doesn’t
have to define a parameter that isn’t used. Behind the scenes, the _Run_ method is implemented through the
Use method, and this feature is provided only as a convenience.

>**Caution** Middleware added to the pipeline after a terminal component will never receive requests. 
>ASP.NET Core won’t warn you if you add a terminal component before the end of the pipeline.

Class-based components can be written so they can be used as both regular and terminal middleware,
```
namespace Platform 
{
	public class QueryStringMiddleWare 
	{
		private RequestDelegate? next;
		public QueryStringMiddleWare() 
		{
			// do nothing
		}
		public QueryStringMiddleWare(RequestDelegate nextDelegate) 
		{
			next = nextDelegate;
		}
		public async Task Invoke(HttpContext context) 
		{
			if (context.Request.Method == HttpMethods.Get && context.Request.Query["custom"] == "true") 
			{
				if (!context.Response.HasStarted) 
				{
					context.Response.ContentType = "text/plain";
				}
				await context.Response.WriteAsync("Class-based Middleware \n");
			}
			if (next != null) 
			{
				await next(context);
			}
		}
	}
}
```
The component will forward requests only when the constructor has been provided with a non-null
value for the nextDelegate parameter. Listing 12-14 shows the application of the component in both
standard and terminal forms.
```
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
((IApplicationBuilder)app).Map("/branch", branch => 
{
	// There is no equivalent to the _UseMiddleware_ method for terminal middleware, so the Run method must
	// be used by creating a new instance of the middleware class and selecting its Invoke method. 
	branch.Run(new Platform.QueryStringMiddleWare().Invoke);
});
app.UseMiddleware<Platform.QueryStringMiddleWare>();
app.MapGet("/", () => "Hello World!");
app.Run();
```
