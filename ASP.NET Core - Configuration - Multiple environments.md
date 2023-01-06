#aspnet_core/Configuration 

---

## Links

[Environments](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-6.0)

## Development and launchSettings.json

The development environment can enable features that shouldn't be exposed in production. For example, the ASP.NET Core project templates enable the [Developer Exception Page](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/error-handling?view=aspnetcore-6.0#developer-exception-page) in the development environment.

The environment for local machine development can be set in the _Properties\launchSettings.json_ file of the project. Environment values set in `launchSettings.json` override values set in the system environment.

The `launchSettings.json` file:

-   Is only used on the local development machine.
-   Is not deployed.
-   Contains profile settings.
 
## Selecting the HTTP Port

### Locally 

Using [launchSettings.json](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-6.0#development-and-launchsettingsjson)

The following JSON shows the `launchSettings.json` file for an ASP.NET Core web project named _EnvironmentsSample_

```json
{
	"iisSettings": {
		"windowsAuthentication": false,
		"anonymousAuthentication": true,
		"iisExpress": {
			"applicationUrl": "http://localhost:59481",
			"sslPort": 44308
		}
	},
	"profiles": {
		"EnvironmentsSample": {
			"commandName": "Project",
			"dotnetRunMessages": true,
			"launchBrowser": true,
			"applicationUrl": "https://localhost:7152;http://localhost:5105",
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

### Globally

Uisng _appsettings.json_ #TODO/AddLink 

```json
{
    "Kestrel": {
        "EndPoints": {
            "Http": {
                "Url": "http://localhost:450"
            }
        }
    },
    "Logging": {
        "LogLevel": {
            "Default": "Information",
            "Microsoft.AspNetCore": "Warning"
        }
    },
    "AllowedHosts": "*"
}
```
