#ASP_NET_CORE/Platform/HostHeader

---

The HTTP specification requires requests to include a *Host* header that specifies the hostname the request is intended for, which makes it possible to support virtual servers where one HTTP server receives requests on a single port and handles them differently based on the hostname that was requested. The default set of middleware that is added to the request pipeline by the `Program` class includes middleware that filters requests based on the *Host* header so that only requests that target a list of approved hostnames are handled and all other requests are rejected.

The default configuration for the Hosts header middleware is included in the appsettings.json file, as follows:
```json
...
{
	"Logging": {
		"LogLevel": {
			"Default": "Information",
			"Microsoft.AspNetCore": "Warning"
		}
	},
	"AllowedHosts": "*",
	"Location": {
		"CityName": "Buffalo"
	}
}
...
```

The *AllowedHosts* configuration property is added to the JSON file when the project is created, and the default value accepts requests regardless of the *Host* header value. You can change the configuration by editing the JSON file. The configuration can also be changed using the options pattern, as shown in Listing 16-20.

> The middleware is added to the pipeline by default, but you can use the UseHostFiltering method if you need to add the middleware explicitly.

Listing 16-20. Configuring Host Header Filtering in the Program.cs File in the Platform Folder
```cs
using Microsoft.AspNetCore.HostFiltering;

var builder = WebApplication.CreateBuilder(args);
builder.Services.Configure<HostFilteringOptions>(opts => 
{
	opts.AllowedHosts.Clear();
	opts.AllowedHosts.Add("*.example.com");
});

var app = builder.Build();
if (!app.Environment.IsDevelopment()) 
{
	app.UseExceptionHandler("/error.html");
	app.UseStaticFiles();
}

app.UseStatusCodePages("text/html", Platform.Responses.DefaultResponse);
app.Use(async (context, next) => 
{
	if (context.Request.Path == "/error") 
	{
		context.Response.StatusCode = StatusCodes.Status404NotFound;
		await Task.CompletedTask;
	}
	else 
	{
		await next();
	}
});

app.Run(context => 
{
	throw new Exception("Something has gone wrong");
});

app.Run();
```

The `HostFilteringOptions` class is used to configure the host filtering middleware using the properties described in Table 16-10.

Table 16-10. The HostFilteringOptions Properties
Name|Description
--|--
AllowedHosts|This property returns a `List<string>` that contains the domains for which requests are allowed. Wildcards are allowed so that *\*.example.com* accepts all names in the *example.com* domain and * accepts all header values.
AllowEmptyHosts|When `false`, this property tells the middleware to reject requests that do not contain a *Host* header. The default value is `true`.
IncludeFailureMessage|When `true`, this property includes a message in the response that indicates the reason for the error. The default value is `true`.

In Listing 16-20, I called the Clear method to remove the wildcard entry that has been loaded from the appsettings.json file and then called the Add method to accept all hosts in the example.com domain. Requests sent from the browser to *localhost* will no longer contain an acceptable *Host* header. You can see what happens by restarting ASP.NET Core and using the browser to request http://localhost:5000. The Host header middleware checks the *Host* header in the request, determines that the request hostname doesn’t match the `AllowedHosts` list, and terminates the request with the 400 status code, which indicates a bad request. Figure 16-13 shows the error message.

Figure 16-13. A request rejected based on the Host header
![[zIMG-ASP.NET-ErrorHandling-16-13.png]]