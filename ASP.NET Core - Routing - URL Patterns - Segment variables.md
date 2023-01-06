#aspnet_core/Routing

---

Segment variables are given a name and are denoted by curly braces (the { and } characters), as shown in Listing 13-8.

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

app.MapGet("capital/uk", new Capital().Invoke);
app.MapGet("population/paris", new Population().Invoke);

app.Run();
```

The URL pattern _{first}/{second}/{third}_ matches URLs whose path contains three segments, regardless of what those segments contain. 
When a segment variable is used, the routing middleware provides the endpoint with the contents of the URL path segment they have matched. 
This content is available through the HttpRequest.RouteValues property, which returns a RouteValuesDictionary object.

Name|Description
--|--
\[key\]|The class defines an indexer that allows values to be retrieved by key.
Keys|This property returns the collection of segment variable names.
Values|This property returns the collection of segment variable values.
Count|This property returns the number of segment variables.
ContainsKey(key)|This method returns true if the route data contains a value for the specified key.
