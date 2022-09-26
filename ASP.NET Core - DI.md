#ASP_NET_CORE/Platform/DI

---

## Preparation

Listing 14-1. The Contents of the IResponseFormatter.cs File in the Services Folder

```cs
namespace Platform.Services 
{
	public interface IResponseFormatter 
	{
		Task Format(HttpContext context, string content);
	}
}
```

The IResponseFormatter interface defines a single method that receives an HttpContext object and a
string. To create an implementation of the interface, add a class called TextResponseFormatter.cs to the
Platform/Services folder with the code shown in Listing 14-2.

Listing 14-2. The Contents of the TextResponseFormatter.cs File in the Services Folder
```cs
namespace Platform.Services 
{
	public class TextResponseFormatter : IResponseFormatter 
	{
		private int responseCounter = 0;
		public async Task Format(HttpContext context, string content) 
		{
			await context.Response.WriteAsync($"Response {++responseCounter}:\n{content}");
		}
	}
}
```

The TextResponseFormatter class implements the interface and writes the content to the response as a
simple string with a prefix to make it obvious when the class is used.

### Creating a Middleware Component and an Endpoint

Some of the examples in this chapter show how features are applied differently when using middleware
and endpoints. Add a file called WeatherMiddleware.cs to the Platform folder with the code shown in
Listing 14-3.

Listing 14-3. The Contents of the WeatherMiddleware.cs File in the Platform Folder

```cs
namespace Platform 
{
	public class WeatherMiddleware 
	{
		private RequestDelegate next;
		public WeatherMiddleware(RequestDelegate nextDelegate) 
		{
			next = nextDelegate;
		}
		public async Task Invoke(HttpContext context) 
		{
			if (context.Request.Path == "/middleware/class") 
			{
				await context.Response.WriteAsync("Middleware Class: It is raining in London");
			}
			else 
			{
				await next(context);
			}
		}
	}
}
```

To create an endpoint that produces a similar result to the middleware component, add a file called
WeatherEndpoint.cs to the Platform folder with the code shown in Listing 14-4.

Listing 14-4. The Contents of the WeatherEndpoint.cs File in the Platform Folder

```cs
namespace Platform 
{
	public class WeatherEndpoint 
	{
		public static async Task Endpoint(HttpContext context) 
		{
			await context.Response.WriteAsync("Endpoint Class: It is cloudy in Milan");
		}
	}
}
```

### Configuring the Request Pipeline

Replace the contents of the Program.cs file with those shown in Listing 14-5. The classes defined in the
previous section are applied alongside lambda functions that produce similar results.

Listing 14-5. Replacing the Contents of the Program.cs File in the Platform Folder

```cs
using Platform;
using Platform.Services;

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.UseMiddleware<WeatherMiddleware>();

IResponseFormatter formatter = new TextResponseFormatter();

app.MapGet("middleware/function", async (context) => 
{
	await formatter.Format(context, "Middleware Function: It is snowing in Chicago");
});
app.MapGet("endpoint/class", WeatherEndpoint.Endpoint);
app.MapGet("endpoint/function", async context => 
{
	await context.Response.WriteAsync("Endpoint Function: It is sunny in LA");
});

app.Run();
```
