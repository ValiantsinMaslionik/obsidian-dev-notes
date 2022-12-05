#ASP_NET_CORE/Platform/HTTPS

---

Users increasingly expect web applications to use HTTPS connections, even for requests that don’t contain or return sensitive data. ASP.NET Core supports both HTTP and HTTPS connections and provides middleware that can force HTTP clients to use HTTPS.

> +++ HTTPS VS SSL VS TLS

HTTPS is the combination of HTTP and the Transport Layer Security (TLS) or Secure Sockets Layer (SSL). TLS has replaced the obsolete SSL protocol, but the term SSL has become synonymous with secure networking and is often used when TLS is actually being used. If you are interested in security and cryptography, then the details of HTTPS are worth exploring, and https://en.wikipedia.org/wiki/HTTPS is a good place to start.

## Enabling HTTPS Connections

HTTPS is enabled and configured in the launchSettings.json file in the Properties folder, as shown in Listing 16-9.
```json
{
	"iisSettings": {
		"windowsAuthentication": false,
		"anonymousAuthentication": true,
		"iisExpress": {
			"applicationUrl": "http://localhost:5000",
			"sslPort": 0
		}
	},
	"profiles": {
		"Platform": {
			"commandName": "Project",
			"dotnetRunMessages": true,
			"launchBrowser": true,
			"applicationUrl": "http://localhost:5000;https://localhost:5500",
			"environmentVariables": {
				"ASPNETCORE_ENVIRONMENT": "Development"
			}
		},
		"IIS Express": {
			"commandName": "IISExpress",
			"launchBrowser": true,
			"environmentVariables": {
				"ASPNETCORE_ENVIRONMENT": "Development"
			}
		}
	}
}
```

The new `applicationUrl` setting sets the URLs to which the application will respond, and HTTPS is enabled by adding an HTTPS URL to the configuration setting. **Note that the URLs are separated by a semicolon and no spaces are allowed**.

The .NET Core runtime includes a test certificate that is used for HTTPS requests. Run the commands shown in Listing 16-10 in the Platform folder to regenerate and trust the test certificate.
```ps
dotnet dev-certs https –clean
dotnet dev-certs https --trust
```

Select *Yes* to the prompts to delete the existing certificate that has already been trusted and then select *Yes* to trust the new certificate, as shown in Figure 16-6.

## Detecting HTTPS Requests

Requests made using HTTPS can be detected through the `HttpRequest.IsHttps` property. In Listing 16-11, I added a message to the fallback response that reports whether a request is made using HTTPS.
```cs
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDistributedMemoryCache();
builder.Services.AddSession(opts => 
{
	opts.IdleTimeout = TimeSpan.FromMinutes(30);
	opts.Cookie.IsEssential = true;
});

var app = builder.Build();
app.UseSession();
app.UseMiddleware<Platform.ConsentMiddleware>();

app.MapGet("/session", async context => 
{
	int counter1 = (context.Session.GetInt32("counter1") ?? 0) + 1;
	int counter2 = (context.Session.GetInt32("counter2") ?? 0) + 1;
	context.Session.SetInt32("counter1", counter1);
	context.Session.SetInt32("counter2", counter2);
	await context.Session.CommitAsync();
	await context.Response.WriteAsync($"Counter1: {counter1}, Counter2: {counter2}");
});

app.MapFallback(async context => 
{
	await context.Response.WriteAsync($"HTTPS Request: {context.Request.IsHttps} \n");
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```

To test HTTPS, restart ASP.NET Core and navigate to http://localhost:5000. This is a regular HTTP request and will produce the result shown on the left of Figure 16-7. Next, navigate to https://localhost:5500, paying close attention to the URL scheme, which is *https* and not *http*, as it has been in previous examples. The new middleware will detect the HTTPS connection and produce the output on the right of Figure 16-7.

## Enforcing HTTPS Requests

ASP.NET Core provides a middleware component that enforces the use of HTTPS by sending a redirection response for requests that arrive over HTTP. Listing 16-12 adds this middleware to the request pipeline. 
```cs
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDistributedMemoryCache();
builder.Services.AddSession(opts => 
{
	opts.IdleTimeout = TimeSpan.FromMinutes(30);
	opts.Cookie.IsEssential = true;
});

var app = builder.Build();
app.UseHttpsRedirection();
app.UseSession();
app.UseMiddleware<Platform.ConsentMiddleware>();

app.MapGet("/session", async context => 
{
	int counter1 = (context.Session.GetInt32("counter1") ?? 0) + 1;
	int counter2 = (context.Session.GetInt32("counter2") ?? 0) + 1;
	context.Session.SetInt32("counter1", counter1);
	context.Session.SetInt32("counter2", counter2);
	await context.Session.CommitAsync();
	await context.Response.WriteAsync($"Counter1: {counter1}, Counter2: {counter2}");
});

app.MapFallback(async context => 
{
	await context.Response.WriteAsync($"HTTPS Request: {context.Request.IsHttps} \n");
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```

The `UseHttpsRedirection` method adds the middleware component, which appears at the start of the request pipeline so that the redirection to HTTPS occurs before any other component can short-circuit the pipeline and produce a response using regular HTTP.

> +++ CONFIGURING HTTPS REDIRECTION

The options pattern can be used to configure the HTTPS redirection middleware, by calling the `AddHttpsRedirection` method like this:
```cs
builder.Services.AddHttpsRedirection(opts => 
{
	opts.RedirectStatusCode = StatusCodes.Status307TemporaryRedirect;
	opts.HttpsPort = 443;
});
```
The only two configuration options are shown in this fragment, which sets the status code used in the redirection response, and the port to which the client is redirected, overriding the value that is loaded from the configuration files. Specifying the HTTPS port can be useful when deploying the application, but care should be taken when changing the redirection status code.

## Enabling HTTP Strict Transport Security

[HSTS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security#:~:text=The%20HTTP%20Strict%2DTransport%2DSecurity,automatically%20be%20converted%20to%20HTTPS.)

One limitation of HTTPS redirection is that the user can make an initial request using HTTP before being redirected to a secure connection, presenting a security risk. The *HTTP Strict Transport Security (HSTS)* protocol is intended to help mitigate this risk and works by
including a header in responses that tells browsers to use HTTPS only when sending requests to the web application’s host. After an HSTS header has been received, browsers that support HSTS will send requests to the application using HTTPS even if the user specifies an HTTP URL. 

Listing 16-13 shows the addition of the HSTS middleware to the request pipeline.
```cs
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddDistributedMemoryCache();
builder.Services.AddSession(opts => 
{
	opts.IdleTimeout = TimeSpan.FromMinutes(30);
	opts.Cookie.IsEssential = true;
});

builder.Services.AddHsts(opts => 
{
	opts.MaxAge = TimeSpan.FromDays(1);
	opts.IncludeSubDomains = true;
});

var app = builder.Build();
if (app.Environment.IsProduction()) 
{
	app.UseHsts();
}

app.UseHttpsRedirection();
app.UseSession();
app.UseMiddleware<Platform.ConsentMiddleware>();

app.MapGet("/session", async context => 
{
	int counter1 = (context.Session.GetInt32("counter1") ?? 0) + 1;
	int counter2 = (context.Session.GetInt32("counter2") ?? 0) + 1;
	context.Session.SetInt32("counter1", counter1);
	context.Session.SetInt32("counter2", counter2);
	await context.Session.CommitAsync();
	await context.Response.WriteAsync($"Counter1: {counter1}, Counter2: {counter2}");
});

app.MapFallback(async context => 
{
	await context.Response.WriteAsync($"HTTPS Request: {context.Request.IsHttps} \n");
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```

The middleware is added to the request pipeline using the `UseHsts` method. The HSTS middleware can be configured with the `AddHsts` method, using the properties described in Table 16-9.

Name|Description
--|--
ExcludeHosts|This property returns a `List<string>` that contains the hosts for which the middleware won’t send an HSTS header. The defaults exclude `localhost` and the loopback addresses for IP version 4 and version 6.
IncludeSubDomains|When `true`, the browser will apply the HSTS setting to subdomains. The default value is `false`.
MaxAge|This property specifies the time period for which the browser should make only HTTPS requests. The default value is 30 days.
Preload|This property is set to true for domains that are part of the HSTS preload scheme. The domains are hard-coded into the browser, which avoids the initial insecure request and ensures that only HTTPS is used. See [hstspreload.org](hstspreload.org) for more details.

HSTS is disabled during development and enabled only in production, which is why the `UseHsts` method is called only for that environment.

> ![[zICO - Warning - 16.png]] HSTS must be used with care because it is easy to create a situation where clients cannot access the application, especially when nonstandard ports are used for HTTP and HTTPS.

If the example application is deployed to a server named myhost, for example, and the user requests http://myhost:5000, the browser will be redirected to https://myhost:5500 and sent the HSTS header, and the application will work as expected. But the next time the user requests http://myhost:5000, they will receive an error stating that a secure connection cannot be established. This problem arises because some browsers take a simplistic approach to HSTS and assume that HTTP requests are handled on port 80 and HTTPS requests on port 443. When the user requests http://myhost:5000, the browser checks its HSTS data and sees that it previously received an HSTS header for myhost. Instead of the HTTP URL that the user entered, the browser sends a request to https://myhost:5000. ASP.NET Core doesn’t handle HTTPS on the port it uses for HTTP, and the request fails. The browser doesn’t remember or understand the redirection it previously received for port 5001. This isn’t an issue where port 80 is used for HTTP and 443 is used for HTTPS. The URL http://myhost is equivalent to http://myhost:80, and https://myhost is equivalent to https://myhost:443, which means that changing the scheme targets the right port. Once a browser has received an HSTS header, it will continue to honor it for the duration of the header’s MaxAge property. When you first deploy an application, it is a good idea to set the HSTS MaxAge property to a relatively short duration until you are confident that your HTTPS infrastructure is working correctly, **which is why I have set MaxAge to one day in Listing 16-13**. Once you are sure that clients will not need to make HTTP requests, you can increase the MaxAge property. 

> A MaxAge value of one year is commonly used.

> If you are testing HSTS with Google Chrome, you can inspect and edit the list of domains to which HSTS is applied by navigating to chrome://net-internals/#hsts.