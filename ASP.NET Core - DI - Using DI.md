#ASP_NET_CORE/Platform/DI 

---

Dependency injection provides an alternative approach to providing services that tidy up the rough edges
that arise in the singleton and type broker patterns, and is integrated with other ASP.NET Core features.

```cs
using Platform;
using Platform.Services;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddSingleton<IResponseFormatter, HtmlResponseFormatter>();

var app = builder.Build();
app.UseMiddleware<WeatherMiddleware>();

app.MapGet("middleware/function", async (HttpContext context, IResponseFormatter formatter) => 
{
	await formatter.Format(context,"Middleware Function: It is snowing in Chicago");
});
app.MapGet("endpoint/class", WeatherEndpoint.Endpoint);
app.MapGet("endpoint/function", async (HttpContext context, IResponseFormatter formatter) => 
{
	await formatter.Format(context, "Endpoint Function: It is sunny in LA");
});

app.Run();
```

Services are registered using extension methods defined by the IServiceCollection interface, an
implementation of which is obtained using the WebApplicationBuilder.Services property. In the listing, I
used an extension method to create a service for the IResponseFormatter interface:

The call to the AddSingleton method told the dependency injection system that a dependency on the
IResponseFormatter interface can be resolved with an HtmlResponseFormatter object. The object is created
and used as an argument to invoke the handler function. Because the object that resolves the dependency
is provided from outside the function that uses it, it is said to have been injected, which is why the process is
known as **dependency injection**.

## Using a Service in a Middleware Class

