#ASP_NET_Core/Platform/Configuration 

---

One of the built-in features provided by ASP.NET Core is access to the application’s configuration settings, which is presented as a service.
The main source of configuration data is the appsettings.json file.

```json
{
	"Logging": {
		"LogLevel": {
			"Default": "Information",
			"Microsoft.AspNetCore": "Warning"
		}
	},
	"AllowedHosts": "*"
}
```

The configuration service will process the JSON configuration file and create nested configuration sections that contain individual settings. For the appsettings.json file in the example application, the configuration service will create a Logging configuration section that contains a LogLevel section.

The *LogLevel* section will contain settings for _Default_, _Microsoft_, and _Microsoft.Hosting.Lifetime_. 
There will also be an *AllowedHosts* setting that isn’t part of a configuration section and whose value is an asterisk (the * character).

> The configuration service doesn’t understand the meaning of the configuration sections or settings in the appsettings.json file and is just responsible for processing the JSON data file and merging the configuration settings with the values obtained from other sources, such as environment variables or command-line arguments. 

## Understanding the Environment-Specific Configuration File

Most projects contain more than one JSON configuration file, allowing different settings to be defined for different parts of the development cycle. There are three predefined environments, named _Development_, _Staging_, and _Production_, each of which corresponds to a commonly used phase of development. During
startup, the configuration service looks for a JSON file whose name includes the current environment. 
The default environment is _Development_, which means the configuration service will load the _appsettings.Development.json_ file and use its contents to supplement the contents of the main appsettings.json file.

Here are the configuration settings added to the appsettings.Development.json file in Listing 15-2:

```json
{
	"Logging": {
		"LogLevel": {
			"Default": "Debug",
			"System": "Information",
			"Microsoft": "Information"
		}
	}
}
```

Where the same setting is defined in both files, the value in the _appsettings.Development.json_ file will replace the one in the _appsettings.json_ file, which means that the contents of the two JSON files will produce the hierarchy of configuration settings shown in Figure 15-3.

![[zIMG-NET-configuration-environment-specific.png]]

The effect of the additional configuration settings is to increase the detail level of logging messages, which I describe in more detail in the “Using the Logging Service” section.

## Accessing Configuration Settings

The configuration data is accessed through a service. If you only require the configuration data to configure middleware, then the dependency on the configuration service can be declared using a parameter, as shown in Listing 15-4.

```cs
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("config", async (HttpContext context, IConfiguration config) => 
{
	string defaultDebug = config["Logging:LogLevel:Default"];
	await context.Response.WriteAsync($"The config setting is: {defaultDebug}");
});

app.MapGet("/", async context => 
{
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```

Configuration data is provided through the [IConfiguration interface](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.configuration.iconfiguration?view=dotnet-plat-ext-6.0); this interface is defined in the _Microsoft.Extensions.Configuration_ namespace and provides an API for navigating through the configuration hierarchy and reading configuration settings.

## Using the Configuration Data in the Program.cs File

The _WebApplication_ and _WebApplicationBuilder_ classes provide a _Configuration_ property that can be used to obtain an implementation of the _IConfiguration_ interface, which is useful when using configuration data to configure an application’s services. Listing 15-6 shows both uses of configuration data.

```cs
var builder = WebApplication.CreateBuilder(args);

var servicesConfig = builder.Configuration;
// - use configuration settings to set up services

var app = builder.Build();

var pipelineConfig = app.Configuration;
// - use configuration settings to set up pipeline

app.MapGet("config", async (HttpContext context, IConfiguration config) => 
{
	string defaultDebug = config["Logging:LogLevel:Default"];
	await context.Response.WriteAsync($"The config setting is: {defaultDebug}");
});

app.MapGet("/", async context => 
{
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```

## Using Configuration Data with the Options Pattern

In Chapter 12, I described the options pattern, which is a useful way to configure middleware components. A helpful feature provided by the _IConfiguration_ service is the ability to create options directly from configuration data.

To prepare, add the configuration settings shown in Listing 15-7 to the appsettings.json file.

```json
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
```

The Location section of the configuration file can be used to provide options pattern values, as shown in Listing 15-8.

> The configuration data is obtained using the GetSection method and passed to the Configure method when the options are created. The configuration values in the selected section are inspected and used to replace the default values with the same names in the options class.

```cs
using Platform;

var builder = WebApplication.CreateBuilder(args);
var servicesConfig = builder.Configuration;
builder.Services.Configure<MessageOptions>(servicesConfig.GetSection("Location"));

var app = builder.Build();

var pipelineConfig = app.Configuration;
// - use configuration settings to set up pipeline

app.UseMiddleware<LocationMiddleware>();
app.MapGet("config", async (HttpContext context, IConfiguration config) => 
{
	string defaultDebug = config["Logging:LogLevel:Default"];
	await context.Response.WriteAsync($"The config setting is: {defaultDebug}");
});

app.MapGet("/", async context => 
{
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```
