#aspnet_core/Configuration 

---

The ASP.NET Core platform provides the `IWebHostEnvironment` service for determining the current environment, which avoids the need to get the configuration setting manually. The `IWebHostEnvironment` service defines the property and methods shown in Table 15-3. The methods are extension methods that are defined in the `Microsoft.Extensions.Hosting` namespace.

Name|Description
--|--
EnvironmentName|This property returns the current environment.
IsDevelopment()|This method returns true when the Development environment has been selected.
IsStaging()|This method returns true when the Staging environment has been selected.
IsProduction()|This method returns true when the Production environment has been selected.
IsEnvironment(env)|This method returns true when the environment specified by the argument has been selected.

If you need to access the environment when setting up services, then you can use the `WebApplicationBuilder.Envirionment` property.
If you need to access the environment when configuring the pipeline, you can use the `WebApplication.Envirionment` property.
If you need to access the environment within a middleware component or endpoint, then you can define a `IWebHostEnvironment` parameter.
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
});

app.MapGet("/", async context => {
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```
