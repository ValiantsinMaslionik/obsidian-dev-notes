#ASP_NET_CORE/Platform/ErrorHandling

---

When the request pipeline is created, the `WebApplicationBuilder` class uses the *development* environment to enable middleware that handles exceptions by producing HTTP responses that are helpful to developers.
Here is a fragment of code from the `WebApplicationBuilder` class:
```cs
...
if (context.HostingEnvironment.IsDevelopment()) 
{
	app.UseDeveloperExceptionPage();
}
...
```

The `UseDeveloperExceptionPage` method adds the middleware component that intercepts exceptions and presents a more useful response. To demonstrate the way that exceptions are handled, Listing 16-14 replaces the middleware and endpoints used in earlier examples with a new component that deliberately throws an exception.

Listing 16-14. Adding a Middleware Component in the Program.cs File in the Platform Folder
```cs
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.Run(context => 
{
	throw new Exception("Something has gone wrong");
});

app.Run();
```

Restart ASP.NET Core and navigate to http://localhost:5000 to see the response that the middleware component generates, which is shown in Figure 16-9. The page presents a stack trace and details about the request, including details of the headers and cookies it contained.

Figure 16-9. The developer exception page Returning an HTML Error Response
![[zIMG-ASP.NET-ErrorHandling-16-9.png]]

When the developer exception middleware is disabled, as it will be when the application is in production, ASP.NET Core deals with unhandled exceptions by sending a response that contains just an error code.

Listing 16-15. Changing Environment in the launchSettings.json File in the Platform/Properties Folder
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
				"ASPNETCORE_ENVIRONMENT": "Production"
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

Start ASP.NET Core using the dotnet run command and navigate to http://localhost:5000. The response you see will depend on your browser because ASP.NET Core has only provided it with a response containing status code 500, without any content to display. Figure 16-10 shows how this is handled by Google Chrome.

Figure 16-10. Returning an error response
![[zIMG-ASP.NET-ErrorHandling-16-10.png]]

As an alternative to returning just status codes, ASP.NET Core provides middleware that intercepts unhandled exceptions and sends a redirection to the browser instead, which can be used to show a friendlier response than the raw status code. The exception redirection middleware is added with the `UseExceptionHandler` method, as shown in Listing 16-16.

Listing 16-16. Returning an HTML Error Response in the Program.cs File in the Platform Folder
```cs
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

if (!app.Environment.IsDevelopment()) 
{
	app.UseExceptionHandler("/error.html");
	app.UseStaticFiles();
}

app.Run(context => 
{
	throw new Exception("Something has gone wrong");
});

app.Run();
```

When an exception is thrown, the exception handler middleware will intercept the response and redirect the browser to the URL provided as the argument to the `UseExceptionHandler` method. For this example, the redirection is to a URL that will be handled by a static file, so the `UseStaticFiles` middleware has also been added to the pipeline. 
To add the file that the browser will receive, create an HTML file named *error.html* in the *wwwroot* folder and add the content shown in Listing 16-17.

Listing 16-17. The Contents of the error.html File in the Platform/wwwroot Folder
```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<link rel="stylesheet" href="/lib/bootstrap/css/bootstrap.min.css" />
		<title>Error</title>
	</head>
	<body class="text-center">
		<h3 class="p-2">Something went wrong...</h3>
		<h6>You can go back to the <a href="/">homepage</a> and try again</h6>
	</body>
</html>
```

Restart ASP.NET Core and navigate to http://localhost:5000 to see the effect of the new middleware. Instead of the raw status code, the browser will be sent the content of the */error.html* URL, as shown in Figure 16-11.

Figure 16-11. Displaying an HTML error
![[zIMG-ASP.NET-ErrorHandling-16-11.png]]

There are versions of the `UseExceptionHandler` method that allow more complex responses to be composed, but my advice is to keep error handling as simple as possible because you can’t anticipate all of the problems an application may encounter, and you run the risk of encountering another exception when trying to handle the one that triggered the handler, resulting in a confusing response or no response at all.

## Enriching Status Code Responses

Not all error responses will be the result of uncaught exceptions. Some requests cannot be processed for reasons other than software defects, such as requests for URLs that are not supported or that require authentication. For this type of problem, redirecting the client to a different URL can be problematic because some clients rely on the error code to detect problems. You will see examples of this in later chapters when I show you how to create and consume RESTful web applications.
ASP.NET Core provides middleware that adds user-friendly content to error responses without requiring redirection. This preserves the error status code while providing a human-readable message that helps users make sense of the problem.
The simplest approach is to define a string that will be used as the body for the response. This is more awkward than simply pointing at a file, but it is a more reliable technique, and as a rule, simple and reliable techniques are preferable when handling errors. To create the string response for the example project, add a class file named *ResponseStrings.cs* to the *Platform* folder with the code shown in Listing 16-18.

Listing 16-18. The Contents of the ResponseStrings.cs File in the Platform Folder
```cs
namespace Platform 
{
	public static class Responses 
	{
		public static string DefaultResponse = @"
			<!DOCTYPE html>
			<html lang=""en"">
				<head>
					<link rel=""stylesheet""
					href=""/lib/bootstrap/css/bootstrap.min.css"" />
					<title>Error</title>
				</head>
				<body class=""text-center"">
					<h3 class=""p-2"">Error {0}</h3>
					<h6>You can go back to the <a href=""/"">homepage</a> and try again</h6>
				</body>
			</html>";
	}
}
```

The `Responses` class defines a `DefaultResponse` property to which I have assigned a multiline string containing a simple HTML document. There is a placeholder—{0}—into which the response status code will be inserted when the response is sent to the client.
Listing 16-19 adds the status code middleware to the request pipeline and adds a new middleware component that will return a 404 status code, indicating that the requested URL was not found.

Listing 16-19. Adding Status Code Middleware in the Program.cs File in the Platform Folder
```cs
var builder = WebApplication.CreateBuilder(args);
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

The `UseStatusCodePages` method adds the response-enriching middleware to the request pipeline. The first argument is the value that will be used for the response’s *Content-Type* header, which is *text/html* in this example. The second argument is the string that will be used as the body of the response, which is the HTML string from Listing 16-18.

![[zIMG-ASP.NET-ErrorHandling-16-12.png]]

The custom middleware component sets the `HttpResponse.StatusCode` property to specify the status code for the response, using a value defined by the `StatusCode` class. 

> ![[zICO - Warning - 16.png]] Middleware components are required to return a `Task`, so I have used the `Task.CompletedTask` property because there is no work for this middleware component to do.

**The status code middleware responds only to status codes between 400 and 600 and doesn’t alter responses that already contain content**, which means you won’t see the response in the figure if an error occurs after another middleware component has started to generate a response. The status code middleware won’t respond to unhandled exceptions because exceptions disrupt the flow of a request through the pipeline, meaning that the status code middleware isn’t given the opportunity to inspect the response before it is sent to the client. As a result, the `UseStatusCodePages` method is typically used in conjunction with the `UseExceptionHandler` or `UseDeveloperExceptionPage` method.

> There are two related methods, `UseStatusCodePagesWithRedirects` and `UseStatusCodePagesWithReExecute`, which work by redirecting the client to a different URL or by rerunning the request through the pipeline with a different URL. 
> **In both cases, the original status code may be lost.**