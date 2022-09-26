#ASP_NET_CORE/Platform/Routing

---

The first step is to assign a name to the route that will be the target of the URL that is generated

```cs
using Platform;

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("{first}/{second}/{third}", async context => 
{
	await context.Response.WriteAsync("Request Was Routed\n");
	foreach (var kvp in context.Request.RouteValues) 
	{
		await context.Response.WriteAsync($"{kvp.Key}: {kvp.Value}\n");
	}
});

app.MapGet("capital/{country}", Capital.Endpoint);
// Assign a name to the route
app.MapGet("population/{city}", Population.Endpoint)
	.WithMetadata(new RouteNameMetadata("population"));

app.Run();
```

The _[ WithMetadata](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.routingendpointconventionbuilderextensions.withmetadata?view=aspnetcore-6.0)_ method is used on the result from the _[MapGet](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.endpointroutebuilderextensions.mapget?view=aspnetcore-6.0)_ method to assign metadata to the
route. The only metadata required for generating URLs is a name, which is assigned by passing a new
_[RouteNameMetadata](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.routing.routenamemetadata?view=aspnetcore-6.0)_ object, whose constructor argument specifies the name that will be used to refer to the
route. 

> Naming routes helps to avoid links being generated that target a route other than the one you expect,
> but they can be omitted, in which case the routing system will try to find the best matching route.

Generating an URL

```cs
namespace Platform 
{
	public class Capital 
	{
		public static async Task Endpoint(HttpContext context) 
		{
			string? capital = null;
			string? country = context.Request.RouteValues["country"] as string;
			switch ((country ?? "").ToLower()) 
			{
				case "uk":
					capital = "London";
					break;
				case "france":
					capital = "Paris";
					break;
				case "monaco":
					LinkGenerator? generator = context.RequestServices.GetService<LinkGenerator>();
					string? url = generator?.GetPathByRouteValues(context, "population", new { city = country });
					if (url != null) 
					{
						context.Response.Redirect(url);
					}
					return;
			}
			
			if (capital != null) 
			{
				await context.Response.WriteAsync($"{capital} is the capital of {country}");
			} 
			else 
			{
				context.Response.StatusCode = StatusCodes.Status404NotFound;
			}
		}
	}
}
```

URLs are generated using the _[LinkGenerator](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.routing.linkgenerator?view=aspnetcore-6.0)_ class. 

> ![[zICO - Warning - 16.png]] You can’t just create a new LinkGenerator instance; one must be obtained using the dependency injection feature.
