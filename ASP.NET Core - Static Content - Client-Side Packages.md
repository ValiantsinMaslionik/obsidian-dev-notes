#aspnet_core/StaticContent

---

Static content doesn’t change and is used to provide images, CSS stylesheets, JavaScript files, and anything else on which the application relies but which doesn’t have to be generated for every request. 

> The conventional location for static content in an ASP.NET Core project is the *wwwroot* folder.

To prepare static content to use in the examples for this section, create the *Platform/wwwroot* folder and add to it a file called *static.html*, with the content shown in Listing 15-29. 
```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<title>Static Content</title>
	</head>
	<body>
		<h3>This is static content</h3>
	</body>
</html>
```

The file contains a basic HTML document with just the basic elements required to display a message in the browser.

## Adding the Static Content Middleware

ASP.NET Core provides a middleware component that handles requests for static content, which is added to the request pipeline in Listing 15-30. ^0fd3c9
```cs
using Platform;
using Microsoft.AspNetCore.HttpLogging;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddHttpLogging(opts => 
{
	opts.LoggingFields = HttpLoggingFields.RequestMethod | HttpLoggingFields.RequestPath | HttpLoggingFields.ResponseStatusCode;
});

var app = builder.Build();
app.UseHttpLogging();
app.UseStaticFiles();

app.MapGet("population/{city?}", Population.Endpoint);

app.Run();
```

The `UseStaticFiles` extension method adds the static file middleware to the request pipeline. This middleware responds to requests that correspond to the names of disk files and passes on all other requests to the next component in the pipeline. 
**This middleware is usually added close to the start of the request pipeline** so that other components don’t handle requests that are for static files.

The middleware component returns the content of the requested file and sets the response headers, such as *Content-Type* and *Content-Length*, that describe the content to the browser.

## Changing the Default Options for the Static Content Middleware

When the `UseStaticFiles` method is invoked without arguments, the middleware will use the *wwwroot* folder to locate files that match the path of the requested URL. This behavior can be adjusted by passing a `StaticFileOptions` object to the UseStaticFiles method.

Name|Description
--|--
ContentTypeProvider|This property is used to get or set the `IContentTypeProvider` object that is responsible for producing the MIME type for a file. The default implementation of the interface uses the file extension to determine the content type and supports the most common file types.
DefaultContentType|This property is used to set the default content type if the IContentTypeProvider cannot determine the type of the file.
FileProvider|This property is used to locate the content for requests, as shown in the listing below.
OnPrepareResponse|This property can be used to register an action that will be invoked before the static content response is generated.
RequestPath|This property is used to specify the URL path that the middleware will respond to, as shown in the following listing.
ServeUnknownFileTypes|By default, the static content middleware will not serve files whose content type cannot be determined by the `IContentTypeProvider`. This behavior is changed by setting this property to `true`.

The `FileProvider` and `RequestPath` properties are the most commonly used. The `FileProvider` property is used to select a different location for static content, and the `RequestPath` property is used to specify a URL prefix that denotes requests for static context. 

> There is also a version of the `UseStaticFiles` method that accepts a single string argument, which is used to set the `RequestPath` configuration property. This is a convenient way of adding support for URLs without needing to create an options object.

```cs
using Platform;
using Microsoft.AspNetCore.HttpLogging;
using Microsoft.Extensions.FileProviders;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddHttpLogging(opts => 
{
	opts.LoggingFields = HttpLoggingFields.RequestMethod | HttpLoggingFields.RequestPath | HttpLoggingFields.ResponseStatusCode;
});

var app = builder.Build();
app.UseHttpLogging();
app.UseStaticFiles();

var env = app.Environment;
app.UseStaticFiles(new StaticFileOptions 
{
	FileProvider = new PhysicalFileProvider($"{env.ContentRootPath}/staticfiles"),
	RequestPath = "/files"
});

app.MapGet("population/{city?}", Population.Endpoint);

app.Run();
```

**Multiple instances of the middleware component can be added to the pipeline**, each of which handles a separate mapping between URLs and file locations. In the listing, a second instance of the static files middleware is added to the request pipeline so that requests for URLs that start with */files* will be handled using files from a folder named *staticfiles*. Reading files from the folder is done with an instance of the `PhysicalFileProvider` class, which is responsible for reading disk files. The `PhysicalFileProvider` class requires an absolute path to work with, which I based on the value of the `ContentRootPath` property defined by the `IWebHostEnvironment` interface, which is the same interface used to determine whether the application is running in the *Development* or *Production* environment.

To provide content for the new middleware component to use, create the *Platform/staticfiles* folder and add to it an HTML file named *hello.html* with the content shown in Listing 15-32.
```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<title>Static Content</title>
	</head>
	<body>
		<h3>This is additional static content</h3>
	</body>
</html>
```

## Using Client-Side Packages

Most web applications rely on client-side packages to support the content they generate, using CSS frameworks to style content or JavaScript packages to create rich functionality in the browser. Microsoft provides the *Library Manager* tool, known as *LibMan*, for downloading and managing client-side packages.

### Preparing the Project for Client-Side Packages

Use the command prompt to run the commands shown in Listing 15-33, which remove any existing *LibMan* package and install the version required by this chapter as a global .NET Core tool.
```ps
dotnet tool uninstall --global Microsoft.Web.LibraryManager.Cli
dotnet tool install --global Microsoft.Web.LibraryManager.Cli --version 2.1.113
```

The next step is to create the *LibMan* configuration file, which specifies the repository that will be used to get client-side packages and the directory into which packages will be downloaded. Open a PowerShell command prompt and run the command shown in Listing 15-34 **in the *Platform* folder**.
```ps
libman init -p cdnjs
```

> The *-p* argument specifies the provider that will get packages. I have used *cdnjs*, which selects *cdnjs.com*. The other option is *unpkg*, which selects *unpkg.com*. 
> If you don’t have existing experience with package repositories, then you should start with the *cdnjs* option.

The command in Listing 15-34 creates a file named *libman.json* in the *Platform* folder; the file contains the following settings:
```json
{
	"version": "1.0",
	"defaultProvider": "cdnjs",
	"libraries": []
}
```

> If you are using Visual Studio, you can create and edit the *libman.json* file directly by selecting *Project ➤ Manage Client-Side Libraries*.

### Installing Client-Side Packages

If you are using Visual Studio, you can install client-side packages by right-clicking the *Platform* project item in the *Solution Explorer* and selecting *Add ➤ Client-Side Library* from the pop-up menu. Visual Studio will present a simple user interface that allows packages to be located in the repository and installed into the project. As you type into the *Library* text field, the repository is queried for packages with matching names.

Enter *bootstrap@5.1.3* into the text field, and the popular Bootstrap CSS framework will be selected, as
The part of the name following the @ character specifies the package version, and version 5.1.3 is required for this chapter. Click the Install button to download and install the Bootstrap package. 

Packages can also be installed from the command line. If you are using Visual Studio Code (or simply prefer the command line, as I do), then run the command shown in Listing 15-35 in the *Platform* folder to install the Bootstrap package.
```ps
libman install bootstrap@5.1.3 -d wwwroot/lib/bootstrap
```

> The required version is separated from the package name by the @ character, and the *-d* argument is used to specify where the package will be installed. 

> The *wwwroot/lib* folder is the conventional location for installing client-side packages in ASP.NET Core projects.

### Using a Client-Side Package

Once a client-side package has been installed, its files can be referenced by script or link HTML elements or by using the features provided by the higher-level ASP.NET Core features described in later chapters. For simplicity in this chapter, Listing 15-36 adds a link element to the static HTML file created earlier in this section.
```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<link rel="stylesheet" href="/lib/bootstrap/css/bootstrap.min.css" />
		<title>Static Content</title>
	</head>
	<body>
		<h3 class="p-2 bg-primary text-white">This is static content</h3>
	</body>
</html>
```

When the browser receives and processes the contents of the *static.html* file, it will encounter the link element and send an HTTP request to the ASP.NET Core runtime for the */lib/bootstrap/css/bootstrap.min.css* URL. The original static file middleware component added in [[#^0fd3c9|Listing 15-30]] will receive this request, determine that it corresponds to a file in the *wwwroot* folder, and return its contents, providing the browser with the Bootstrap CSS stylesheet. The Bootstrap styles are applied through the classes to which the `h3` element has been assigned.