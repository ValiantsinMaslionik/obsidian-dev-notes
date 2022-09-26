#ASP_NET_CORE/Platform/Routing

---

The WebApplication class implements the IEndpointRouteBuilder interface, which means that
endpoints can be created more concisely. Behind the scenes, the routing middleware is still responsible for
matching requests and selecting routes.

Notice that I have removed the terminal middleware component from the pipeline. In the previous
example, the routing middleware I added to the pipeline explicitly would forward requests along the
pipeline only if none of the routes matched. Defining routes directly, as in [[#^1a7df9|Listing 13-7]], changes this behavior
so that requests are always forwarded, which means the terminal middleware will be used for every request.

It is important to understand that using the simplified pipeline configuration doesn’t just reduce the amount 
of code in the Program.cs file and can alter the way that requests are processed.

Listing 13-7 ^1a7df9

```cs
using Platform;

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("routing", async context => 
{
	await context.Response.WriteAsync("Request Was Routed");
});

app.MapGet("capital/uk", new Capital().Invoke);
app.MapGet("population/paris", new Population().Invoke);

app.Run();

```
