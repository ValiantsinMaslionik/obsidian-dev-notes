#ASP_NET_CORE/Platform/Routing

---

Endpoints usually rely on the routing middleware to provide specific segment variables, rather than
enumerating all the segment variables. By relying on the URL pattern to provide a specific value, I can
refactor the Capital and Population classes to depend on the route data.

```cs
namespace Platform 
{
	public class Population 
	{
		public static async Task Endpoint(HttpContext context) 
		{
			string? city = context.Request.RouteValues["city"] as string;
			int? pop = null;
			switch ((city ?? "").ToLower()) 
			{
				case "london":
					pop = 8_136_000;
					break;
				case "paris":
					pop = 2_141_000;
					break;
				case "monaco":
					pop = 39_000;
					break;
			}
			if (pop.HasValue) 
			{
				await context.Response.WriteAsync($"City: {city}, Population: {pop}");
			}
			else 
			{
				context.Response.StatusCode = StatusCodes.Status404NotFound;
			}
		}
	}
}
```

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
app.MapGet("population/{city}", Population.Endpoint);

app.Run();
```
