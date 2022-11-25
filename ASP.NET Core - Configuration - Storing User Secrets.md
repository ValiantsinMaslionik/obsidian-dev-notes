#ASP_NET_Core/Platform/Configuration 

---

The user secrets service allows sensitive data to be stored in a file that isn’t part of the project and won’t be checked into version control, allowing each developer to have sensitive data that won’t be accidentally exposed through a version control check-in.

## Storing User Secrets

The first step is to prepare the file that will be used to store sensitive data. Open a new PowerShell command prompt and run the commands shown in Listing 15-13 in the *Platform* folder (the folder that contains the
*Platform.csproj* file).
```ps
dotnet tool uninstall --global dotnet-user-secrets
dotnet tool install --global dotnet-user-secrets --version 3.0.0-preview-18579-0056
```

These commands remove the package if it has been installed previously and install the version required by this chapter. Next, run the command shown in Listing 15-14 in the *Platform* folder.

> If you have problems running the commands in this section, then close the PowerShell prompt and open a new one after installing the global tools package.

```ps
dotnet user-secrets init
```

This command adds an element to the *Platform.csproj* project file that contains a unique ID for the project that will be associated with the secrets on each developer machine. 
Next, run the commands shown in Listing 15-15 in the *Platform* folder.

Each secret has a key and a value, and related secrets can be grouped together by using a common prefix, followed by a colon (the : character), followed by the secret name. 
The commands in Listing 15-15 create related *Id* and *Key* secrets that have the `WebService` prefix.

```ps
dotnet user-secrets set "WebService:Id" "MyAccount"
dotnet user-secrets set "WebService:Key" "MySecret123$"
```

After each command, you will see a message confirming that a secret has been added to the secret store. To check the secrets for the project, use the command prompt to run the command shown in Listing 15-16 in the *Platform* folder.
```ps
dotnet user-secrets list
```

This command produces the following output:
```txt
WebService:Key = MySecret123$
WebService:Id = MyAccount
```

Behind the scenes, a JSON file has been created in the `%APPDATA%\Microsoft\UserSecrets` folder (or the `~/.microsoft/usersecrets` folder for Linux) to store the secrets. Each project has its own folder (whose
name corresponds to the unique ID created by the init command in Listing 15-14).

> If you are using Visual Studio, you can create and edit the JSON file directly by right-clicking the project in the *Solution Explorer* and selecting *Manage User Secrets* from the pop-up menu.

## Reading User Secrets

User secrets are merged with the normal configuration settings and accessed in the same way. In Listing 15-17, I have added a statement that displays the secrets to the middleware component that handles the */config* URL.

```cs
using Platform;

var builder = WebApplication.CreateBuilder(args);
var servicesConfig = builder.Configuration;
builder.Services.Configure<MessageOptions>(servicesConfig.GetSection("Location"));
var servicesEnv = builder.Environment;
// - use environment to set up services

var app = builder.Build();
var pipelineConfig = app.Configuration;
// - use configuration settings to set up pipeline

var pipelineEnv = app.Environment;
// - use envirionment to set up pipeline

app.UseMiddleware<LocationMiddleware>();
app.MapGet("config", async (HttpContext context, IConfiguration config, IWebHostEnvironment env) => 
{
	string defaultDebug = config["Logging:LogLevel:Default"];
	await context.Response.WriteAsync($"The config setting is: {defaultDebug}");
	await context.Response.WriteAsync($"\nThe env setting is: {env.EnvironmentName}");
	
	string wsID = config["WebService:Id"];
	string wsKey = config["WebService:Key"];
	await context.Response.WriteAsync($"\nThe secret ID is: {wsID}");
	await context.Response.WriteAsync($"\nThe secret Key is: {wsKey}");
});

app.MapGet("/", async context => 
{
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```

> ![[zICO - Warning - 16.png]] User secrets are loaded only when the application is set to the *Development* environment. 

Edit the *launchSettings.json* file to change the environment to *Development*, as shown in Listing 15-18.
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
