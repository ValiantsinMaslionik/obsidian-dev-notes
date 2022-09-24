#ASP_NET_CORE/Platform 

---

The Program.cs file contains the code statements that are executed when the application is started and that
are used to configure the ASP.NET platform and the individual frameworks it supports. Here is the content of
the Program.cs file in the example project:

> This file contains only top-level statements. 

```
// This method is responsible for setting up the basic features of the ASP.NET Core platform, including
// creating services responsible for configuration data and logging, both of which are described in Chapter 15.
// This method also sets up the HTTP server, named Kestrel, that is used to receive HTTP requests.
var builder = WebApplication.CreateBuilder(args);

// The result from the CreateBuilder method is a WebApplicationBuilder object, which is used to
// register additional services, although none are defined at present. The WebApplicationBuilder class defines
// a Build method that is used to finalize the initial setup
var app = builder.Build();

// The result of the Build method is a WebApplication object, which is used to set up middleware
// components. The template has set up one middleware component, using the MapGet extension method
app.MapGet("/", () => "Hello World!");

// The final statement in the Program.cs file calls the Run method defined by the WebApplication class,
// which starts listening to HTTP requests.
app.Run();
```

MapGet is an extension method for the IEndpointRouteBuilder interface ([see]([[ASP.NET Core - Endpoints]])), which is implemented by the
WebApplication class, and which sets up a function that will handle HTTP requests with a specified URL
path.  In this case, the function responds to requests for the default URL path, which is denoted by /, and the
function responds to all requests by returning a simple string response

Even though the function used with the MapGet method returns a string, ASP.NET Core is clever enough
to create a valid HTTP response that will be understood by browsers. 

#ASP_NET_CORE/PowerShell

While ASP.NET Core is still running, open a new PowerShell command prompt and run the command shown 
to send an HTTP request to the ASP.NET Core server.

`(Invoke-WebRequest http://localhost:5000).RawContent`

```
HTTP/1.1 200 OK
Transfer-Encoding: chunked
Content-Type: text/plain; charset=utf-8
Date: Thu, 11 Nov 2021 18:08:35 GMT
Server: Kestrel
Hello World!
```