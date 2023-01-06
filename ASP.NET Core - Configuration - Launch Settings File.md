#aspnet_core/Configuration 

---

The *launchSettings.json* file in the *Properties* folder contains the configuration settings for starting the ASP.NET Core platform, including the TCP ports that are used to listen for HTTP and HTTPS requests and the environment used to select the additional JSON configuration files.
Here is the content added to the launchSettings.json file when the project was created and then edited to set the HTTP ports:
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
			"applicationUrl": "http://localhost:5000",
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

Section Name|Description
--|--
iisSettings|Is used to configure the HTTP and HTTPS ports used when the ASP.NET Core platform is started through IIS Express, which is how older versions of ASP.NET Core were deployed.
profiles|Describes a series of launch profiles, which define configuration settings for different ways of running the application. 
Platform|Defines the configuration used by the `dotnet run` command. 
IIS Express|Defines the configuration used when the application is used with IIS Express.
environmentVariables|Is used to define environment variables that are added to the application’s configuration data. There is only one environment variable defined by default: `ASPNETCORE_ENVIRONMENT`.

During startup, the value of the `ASPNETCORE_ENVIRONMENT` setting is used to select the additional JSON configuration file so that a value of *Development*, for example, will cause the *appsettings.Development.json* file to be loaded.

> +++ **Visual Studio Code** 

When the application is started within Visual Studio Code, `ASPNETCORE_ENVIRONMENT` is set in a different file. 
Select *Run ➤ Open Configurations* to open the *launch.json* file in the *.vscode* folder, which is created when a project is edited with Visual Studio Code. 

Here is the default configuration for the example project, showing the current `ASPNETCORE_ENVIRONMENT` value, with the comments removed for brevity:
```json
{
	"version": "0.2.0",
	"configurations": [
		{
			"name": ".NET Core Launch (web)",
			"type": "coreclr",
			"request": "launch",
			"preLaunchTask": "build",
			"program": "${workspaceFolder}/bin/Debug/net6.0/Platform.dll",
			"args": [],
			"cwd": "${workspaceFolder}",
			"stopAtEntry": false,
			"serverReadyAction": {
				"action": "openExternally",
				"pattern": "\\bNow listening on:\\s+(https?://\\S+)"
			},
			"env": {
				"ASPNETCORE_ENVIRONMENT": "Development"
			},
			"sourceFileMap": {
				"/Views": "${workspaceFolder}/Views"
			}
		},
		{
			"name": ".NET Core Attach",
			"type": "coreclr",
			"request": "attach"
		}
	]
}
```

To display the value of the ASPNETCORE_ENVIRONMENT setting, add the statements to the middleware component that responds to the /config URL, as shown in Listing 15-9.
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
	string environ = config["ASPNETCORE_ENVIRONMENT"];
	await context.Response.WriteAsync($"\nThe env setting is: {environ}");
});

app.MapGet("/", async context => 
{
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```

To see the effect that the `ASPNETCORE_ENVIRONMENT` setting has on the overall configuration, change the value in the *launchSettings.json* file, as shown in Listing 15-10.
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
			"applicationUrl": "http://localhost:5000",
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

If you are using Visual Studio, you can change the environment variables by selecting *Debug ➤ Launch Profiles*. The settings for each launch profile are displayed, and there is support for changing the value of the `ASPNETCORE_ENVIRONMENT` variable, as shown in Figure 15-7.

> +++ **Visual Studio Code**

Select *Run ➤ Open Configurations* and change the value in the *env* section, as shown in Listing 15-11.
```json
{
	"version": "0.2.0",
	"configurations": [
		{
			"name": ".NET Core Launch (web)",
			"type": "coreclr",
			"request": "launch",
			"preLaunchTask": "build",
			"program": "${workspaceFolder}/bin/Debug/net6.0/Platform.dll",
			"args": [],
			"cwd": "${workspaceFolder}",
			"stopAtEntry": false,
			"serverReadyAction": {
				"action": "openExternally",
				"pattern": "\\bNow listening on:\\s+(https?://\\S+)"
			},
			"env": {
				"ASPNETCORE_ENVIRONMENT": "Production"
			},
			"sourceFileMap": {
				"/Views": "${workspaceFolder}/Views"
			}
		},
		{
			"name": ".NET Core Attach",
			"type": "coreclr",
			"request": "attach"
		}
	]
}
```

Notice that both configuration values displayed in the browser have changed. The *appsettings.Development.json* file is no longer loaded, and there is no *appsettings.Production.json* file in the project, so only the configuration settings in the *appsettings.json*77 file are used.