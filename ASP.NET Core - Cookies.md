#aspnet_core/Cookies

---

*Cookies* are small amounts of text added to responses that the browser includes in subsequent requests. Cookies are important for web applications because they allow features to be developed that span a series of HTTP requests, each of which can be identified by the cookies that the browser sends to the server. ASP.NET Core provides support for working with cookies through the `HttpRequest` and `HttpResponse` objects that are provided to middleware components. To demonstrate, Listing 16-3 changes the routing configuration in the example application to add endpoints that implement a counter.
```cs
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.MapGet("/cookie", async context => 
{
	int counter1 = int.Parse(context.Request.Cookies["counter1"] ?? "0") + 1;
	context.Response.Cookies.Append("counter1", counter1.ToString(), new CookieOptions 
	{
		MaxAge = TimeSpan.FromMinutes(30)
	});
	
	int counter2 = int.Parse(context.Request.Cookies["counter2"] ?? "0") + 1;
	context.Response.Cookies.Append("counter2", counter2.ToString(), new CookieOptions 
	{
		MaxAge = TimeSpan.FromMinutes(30)
	});
	
	await context.Response.WriteAsync($"Counter1: {counter1}, Counter2: {counter2}");
});

app.MapGet("clear", context => 
{
	context.Response.Cookies.Delete("counter1");
	context.Response.Cookies.Delete("counter2");
	context.Response.Redirect("/");
	return Task.CompletedTask;
});

app.MapFallback(async context => await context.Response.WriteAsync("Hello World!"));

app.Run();
```

The new endpoints rely on cookies called *counter1* and *counter2*. When the */cookie* URL is requested, the middleware looks for the cookies and parses the values to an `int`. If there is no cookie, a fallback zero is used.
Cookies are accessed through the `HttpRequest.Cookies` property, where the name of the cookie is used as the key. The value retrieved from the cookie is incremented and used to set a cookie in the response.
Cookies are set through the `HttpResponse.Cookies` property, and the `Append` method creates or replaces a cookie in the response. The arguments to the `Append` method are the name of the cookie, its value, and a `CookieOptions` object, which is used to configure the cookie. 

Table 16-2 ^f719e6

Name|Description
--|--
Domain|This property specifies the hosts to which the browser will send the cookie. By default, the cookie will be sent only to the host that created the cookie.
Expires|This property sets the expiry for the cookie.
HttpOnly|When true, this property tells the browser not to include the cookie in requests made by JavaScript code.
IsEssential|This property is used to indicate that a cookie is essential, as described in the [[#Managing Cookie Consent]] section.
MaxAge|This property specifies the number of seconds until the cookie expires. Older browsers do not support cookies with this setting.
Path|This property is used to set a URL path that must be present in the request before the cookie will be sent by the browser.
SameSite|This property is used to specify whether the cookie should be included in cross-site requests. The values are `Lax`, `Strict`, and `None` (which is the default value).
Secure|When `true`, this property tells the browser to send the cookie using HTTPS only.

> Cookies are sent in the response header, which means that cookies can be set only before the response body is written, after which any changes to the cookies are ignored.

The only cookie option set in Listing 16-3 is `MaxAge`, which tells the browser that the cookies expire after 30 minutes. The middleware in Listing 16-3 deletes the cookies when the */clear* URL is requested, which is done using the `HttpResponse.Cookie.Delete` method, after which the browser is redirected to the */* URL.

## Enabling Cookie Consent Checking

The EU General Data Protection Regulation (GDPR) requires the user’s consent before nonessential cookies can be used. ASP.NET Core provides support for obtaining consent and preventing nonessential cookies from being sent to the browser when consent has not been granted. The options pattern is used to create a policy for cookies, which is applied by a middleware component, as shown in Listing 16-4.

> Cookie consent is only one part of GDPR. See https://en.wikipedia.org/wiki/General_Data_Protection_Regulation for a good overview of the regulations.

```cs
var builder = WebApplication.CreateBuilder(args);
builder.Services.Configure<CookiePolicyOptions>(opts => 
{
	opts.CheckConsentNeeded = context => true;
});

var app = builder.Build();
app.UseCookiePolicy();

app.MapGet("/cookie", async context => 
{
	int counter1 = int.Parse(context.Request.Cookies["counter1"] ?? "0") + 1;
	context.Response.Cookies.Append("counter1", counter1.ToString(), new CookieOptions 
	{
		MaxAge = TimeSpan.FromMinutes(30),
		IsEssential = true
	});
	
	int counter2 = int.Parse(context.Request.Cookies["counter2"] ?? "0") + 1;
	context.Response.Cookies.Append("counter2", counter2.ToString(), new CookieOptions 
	{
		MaxAge = TimeSpan.FromMinutes(30)
	});
	
	await context.Response.WriteAsync($"Counter1: {counter1}, Counter2: {counter2}");
});

app.MapGet("clear", context => 
{
	context.Response.Cookies.Delete("counter1");
	context.Response.Cookies.Delete("counter2");
	context.Response.Redirect("/");
	return Task.CompletedTask;
});

app.MapFallback(async context => await context.Response.WriteAsync("Hello World!"));

app.Run();
```

The options pattern is used to configure a `CookiePolicyOptions` object, which sets the overall policy for cookies in the application using the properties described in Table 16-3.

Name|Description
--|--
CheckConsentNeeded|This property is assigned a function that receives an HttpContext object and returns true if it represents a request for which cookie consent is required. The function is called for every request, and the default function always returns `false`.
ConsentCookie|This property returns an object that is used to configure the cookie sent to the browser to record the user’s cookie consent.
HttpOnly|This property sets the default value for the HttpOnly property, as described in [[#^f719e6|Table 16-2]].
MinimumSameSitePolicy|This property sets the lowest level of security for the `SameSite` property, as described in [[#^f719e6|Table 16-2]].
Secure|This property sets the default value for the Secure property, as described in [[#^f719e6|Table 16-2]].

To enable consent checking, I assigned a new function to the `CheckConsentNeeded` property that always returns `true`. The function is called for every request that ASP.NET Core receives, which means that sophisticated rules can be defined to select the requests for which consent is required. For this application, I have taken the most cautious approach and required consent for all requests.

The middleware that enforces the cookie policy is added to the request pipeline using the `UseCookiePolicy` method. The result is that only cookies whose `IsEssential` property is `true` will be added to responses. 

### Managing Cookie Consent

Unless the user has given consent, only cookies that are essential to the core features of the web application are allowed. Consent is managed through a request feature, which provides middleware components with access to the implementation details of how requests and responses are handled by ASP.NET Core. Features are accessed through the `HttpRequest.Features` property, and each feature is represented by an interface whose properties and methods deal with one aspect of low-level request handling.

Features deal with aspects of request handling that rarely need to be altered, such as the structure of responses. The exception is the management of cookie consent, which is handled through the `ITrackingConsentFeature` interface, which defines the methods and properties described in Table 16-4.

Name|Description
--|--
CanTrack|This property returns `true` if nonessential cookies can be added to the current request, either because the user has given consent or because consent is not required.
CreateConsentCookie()|This method returns a cookie that can be used by JavaScript clients to indicate consent.
GrantConsent()|Calling this method adds a cookie to the response that grants consent for nonessential cookies.
HasConsent|This property returns `true` if the user has given consent for nonessential cookies.
IsConsentNeeded|This property returns true if consent for nonessential cookies is required for the current request.
WithdrawConsent()|This method deletes the consent cookie.

To deal with consent, add a class file named *ConsentMiddleware.cs* to the *Platform* folder and the code shown in Listing 16-5. Managing cookie consent can be done using lambda expressions, but I have used a class in this example to keep the `Configure` method uncluttered.
```cs
using Microsoft.AspNetCore.Http.Features;

namespace Platform 
{
	public class ConsentMiddleware 
	{
		private RequestDelegate next;
		
		public ConsentMiddleware(RequestDelegate nextDelgate) 
		{
			next = nextDelgate;
		}
		
		public async Task Invoke(HttpContext context) 
		{
			if (context.Request.Path == "/consent") 
			{
				ITrackingConsentFeature? consentFeature = context.Features.Get<ITrackingConsentFeature>();
				if (consentFeature != null) 
				{
					if (!consentFeature.HasConsent) 
					{
						consentFeature.GrantConsent();
					}
					else 
					{
						consentFeature.WithdrawConsent();
					}
					
					await context.Response.WriteAsync(consentFeature.HasConsent ? "Consent Granted \n" : "Consent Withdrawn\n");
				}
			} 
			else 
			{
				await next(context);
			}
		}
	}
}
```


Using the properties and methods described in Table 16-4, the new middleware component responds to the */consent* URL to determine and change the cookie consent. 

Listing 16-6 adds the new middleware to the request pipeline.
```cs
var builder = WebApplication.CreateBuilder(args);
builder.Services.Configure<CookiePolicyOptions>(opts => 
{
	opts.CheckConsentNeeded = context => true;
});

var app = builder.Build();
app.UseCookiePolicy();
app.UseMiddleware<Platform.ConsentMiddleware>();

app.MapGet("/cookie", async context => 
{
	// ...statments omitted for brevity...
```

To see the effect, restart ASP.NET Core and request http://localhost:5000/consent and then http://localhost:5000/cookie. 
When consent is granted, nonessential cookies are allowed, and both the counters in the example will work, as shown in Figure 16-4. 
Repeat the process to withdraw consent, and you will find that only the counter whose cookie has been denoted as essential works.
