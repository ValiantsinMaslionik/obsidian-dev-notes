#aspnet_core/DI 

---

Defining a service and consuming in the same code file may not seem impressive, but once a service
is defined, it can be used almost anywhere in an ASP.NET Core application. Listing 14-14 declares a
dependency on the _IResponseFormatter_ interface in the middleware class defined at the start of the chapter.

```cs
using Platform.Services;

namespace Platform 
{
	public class WeatherMiddleware 
	{
		private RequestDelegate next;
		private IResponseFormatter formatter;
		public WeatherMiddleware(RequestDelegate nextDelegate, IResponseFormatter respFormatter) 
		{
			next = nextDelegate;
			formatter = respFormatter;
		}
		public async Task Invoke(HttpContext context) 
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
