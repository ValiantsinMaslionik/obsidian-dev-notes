#ASP_NET_CORE/Platform 

---

## Options pattern

There is a common pattern for configuring middleware that is known as the options pattern and that is used
by some of the built-in middleware components described in later chapters.

The starting point is to define a class that contains the configuration options for a middleware
component. 

```cs
namespace Platform 
{
	public class MessageOptions 
	{
		public string CityName { get; set; } = "New York";
		public string CountryName{ get; set; } = "USA";
	}
}
```

The MessageOptions class defines properties that detail a city and a country. In Listing 12-16, I have
used the options pattern to create a custom middleware component that relies on the MessageOptions class
for its configuration. I have also removed some of the middleware from previous examples for brevity.

```cs
using Microsoft.Extensions.Options;
using Platform;

var builder = WebApplication.CreateBuilder(args);
builder.Services.Configure<MessageOptions>(options => 
{
	options.CityName = "Albany";
});

var app = builder.Build();
app.MapGet("/location", async (HttpContext context, IOptions<MessageOptions> msgOpts) => 
{
	Platform.MessageOptions opts = msgOpts.Value;
	await context.Response.WriteAsync($"{opts.CityName}, {opts.CountryName}");
});

app.MapGet("/", () => "Hello World!");
app.Run();
```

This statement creates options using the MessageOptions class and changes the value of the CityName
property. When the application starts, the ASP.NET Core platform will create a new instance of the
MessageOptions class and pass it to the function supplied as the argument to the Configure method,
allowing the default option values to be changed.

The options will be available as a service, which means this statement must appear before the call to the
Build method is called, as shown in the listing. Middleware components can access the configuration 
options by defining a parameter for the function that handles the request, like this:

```cs
app.MapGet("/location", async (HttpContext context, IOptions<MessageOptions> msgOpts) => 
{
	Platform.MessageOptions opts = msgOpts.Value;
	await context.Response.WriteAsync($"{opts.CityName}, {opts.CountryName}");
});
```

Some of the extension methods used to register middleware components will accept any function to
handle requests. When a request is processed, the ASP.NET Core platform inspects the function to find
parameters that require services, which allows the middleware component to use the configuration options
in the response it generates:

```cs
app.MapGet("/location", async (HttpContext context, IOptions<MessageOptions> msgOpts) => 
{
	Platform.MessageOptions opts = msgOpts.Value;
	await context.Response.WriteAsync($"{opts.CityName}, {opts.CountryName}");
});
```

## Options patter in a class-based middleware

The options pattern can also be used with class-based middleware and is applied in a similar way. Add the
statements shown in Listing 12-17 to the Middleware.cs file to define a class-based middleware component
that uses the MessageOptions class for configuration.

```cs
using Microsoft.Extensions.Options;
namespace Platform 
{
	public class QueryStringMiddleWare 
	{
		private RequestDelegate? next;
		// ...statements omitted for brevity...
	}
	public class LocationMiddleware 
	{
		private RequestDelegate next;
		private MessageOptions options;
		public LocationMiddleware(RequestDelegate nextDelegate, IOptions<MessageOptions> opts) 
		{
			next = nextDelegate;
			options = opts.Value;
		}
		public async Task Invoke(HttpContext context) 
		{
			if (context.Request.Path == "/location") 
			{
				await context.Response.WriteAsync($"{options.CityName}, {options.CountryName}");
			}
			else 
			{
				await next(context);
			}
		}
	}
}
```

The LocationMiddleware class defines an IOptions&gt;MessageOptions&lt; constructor parameter, which
can be used in the Invoke method to access the options settings.
Listing 12-18 reconfigures the request pipeline to replace the lambda function middleware component
with the class from Listing 12-17.

```cs
using Platform;
var builder = WebApplication.CreateBuilder(args);
builder.Services.Configure<MessageOptions>(options => 
{
	options.CityName = "Albany";
});
var app = builder.Build();
app.UseMiddleware<LocationMiddleware>();
app.MapGet("/", () => "Hello World!");
app.Run();
```

When the UseMiddleware statement is executed, the LocationMiddleware constructor is inspected,
and its IOptions&gt;MessageOptions^lt; parameter will be resolved using the object created with the Services.
Configure method. 