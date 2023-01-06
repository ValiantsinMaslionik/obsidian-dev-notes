#aspnet_core/Routing 

---

Fallback routes direct a request to an endpoint only when no other route matches a request. Fallback routes
prevent requests from being passed further along the request pipeline by ensuring that the routing system
will always generate a response, as shown in Listing 13-23.

```cs
using Platform;
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.MapGet("{first:alpha:length(3)}/{second:bool}", async context => 
{
	await context.Response.WriteAsync("Request Was Routed\n");
	foreach (var kvp in context.Request.RouteValues) 
	{
		await context.Response.WriteAsync($"{kvp.Key}: {kvp.Value}\n");
	}
});

app.MapGet("capital/{country:regex(^uk|france|monaco$)}", Capital.Endpoint);
app.MapGet("size/{city?}", Population.Endpoint).WithMetadata(new RouteNameMetadata("population"));

app.MapFallback(async context => 
{
	await context.Response.WriteAsync("Routed to fallback endpoint");
});

app.Run();
```

The MapFallback method creates a route that will be used as a last resort and that will match any request. 

Name|Description
--|--
MapFallback(endpoint)|This method creates a fallback that routes requests to an endpoint.
MapFallbackToFile(path)|This method creates a fallback that routes requests to a file.
