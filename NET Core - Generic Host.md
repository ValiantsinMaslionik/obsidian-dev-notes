#net_core/hosting 

---

[Learn.MS](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host)

The Worker Service templates create a .NET Generic Host, [HostBuilder](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostbuilder). The Generic Host can be used with other types of .NET applications, such as Console apps.

A _host_ is an object that encapsulates an app's resources and lifetime functionality, such as:

-   Dependency injection (DI)
-   Logging
-   Configuration
-   App shutdown
-   `IHostedService` implementations

When a host starts, it calls [IHostedService.StartAsync](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihostedservice.startasync) on each implementation of [IHostedService](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihostedservice) registered in the service container's collection of hosted services. In a worker service app, all `IHostedService` implementations that contain [BackgroundService](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.backgroundservice) instances have their [BackgroundService.ExecuteAsync](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.backgroundservice.executeasync) methods called.

The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown. This is achieved with the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) NuGet package.

## [](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#set-up-a-host)Set up a host

The host is typically configured, built, and run by code in the `Program` class. The `Main` method:

-   Calls a [CreateDefaultBuilder()](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.host.createdefaultbuilder#microsoft-extensions-hosting-host-createdefaultbuilder) method to create and configure a builder object.
-   Calls [Build()](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihostbuilder.build#microsoft-extensions-hosting-ihostbuilder-build) to create an [IHost](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihost) instance.
-   Calls [Run](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostingabstractionshostextensions.run) or [RunAsync](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostingabstractionshostextensions.runasync) method on the host object.

The .NET Worker Service templates generate the following code to create a Generic Host:

```cs
IHost host = Host.CreateDefaultBuilder(args)
    .ConfigureServices((hostContext, services) =>
    {
        services.AddHostedService<Worker>();
    })
    .Build();

host.Run();
```

## [](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#default-builder-settings)Default builder settings

The [CreateDefaultBuilder](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.host.createdefaultbuilder) method:

-   Sets the content root to the path returned by [GetCurrentDirectory()](https://learn.microsoft.com/en-us/dotnet/api/system.io.directory.getcurrentdirectory#system-io-directory-getcurrentdirectory).
-   Loads [host configuration](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#host-configuration) from:
    -   Environment variables prefixed with `DOTNET_`.
    -   Command-line arguments.
-   Loads app configuration from:
    -   _appsettings.json_.
    -   _appsettings.{Environment}.json_.
    -   Secret Manager when the app runs in the `Development` environment.
    -   Environment variables.
    -   Command-line arguments.
-   Adds the following logging providers:
    -   Console
    -   Debug
    -   EventSource
    -   EventLog (only when running on Windows)
-   Enables scope validation and [dependency validation](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validateonbuild#microsoft-extensions-dependencyinjection-serviceprovideroptions-validateonbuild) when the environment is `Development`.

The `ConfigureServices` method exposes the ability to add services to the [Microsoft.Extensions.DependencyInjection.IServiceCollection](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection) instance. Later, these services can be made available from dependency injection.

## [](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#framework-provided-services)Framework-provided services

The following services are registered automatically:

-   [IHostApplicationLifetime](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#ihostapplicationlifetime)
-   [IHostLifetime](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#ihostlifetime)
-   [IHostEnvironment](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#ihostenvironment)

## [](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#ihostapplicationlifetime)IHostApplicationLifetime

Inject the [IHostApplicationLifetime](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime) service into any class to handle post-startup and graceful shutdown tasks. Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods. The interface also includes a [StopApplication()](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.stopapplication#microsoft-extensions-hosting-ihostapplicationlifetime-stopapplication) method.

The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:

```cs
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

namespace AppLifetime.Example;

public sealed class ExampleHostedService : IHostedService
{
    private readonly ILogger _logger;

    public ExampleHostedService(
        ILogger<ExampleHostedService> logger,
        IHostApplicationLifetime appLifetime)
    {
        _logger = logger;

        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);
    }

    public Task StartAsync(CancellationToken cancellationToken)
    {
        _logger.LogInformation("1. StartAsync has been called.");

        return Task.CompletedTask;
    }

    public Task StopAsync(CancellationToken cancellationToken)
    {
        _logger.LogInformation("4. StopAsync has been called.");

        return Task.CompletedTask;
    }

    private void OnStarted()
    {
        _logger.LogInformation("2. OnStarted has been called.");
    }

    private void OnStopping()
    {
        _logger.LogInformation("3. OnStopping has been called.");
    }

    private void OnStopped()
    {
        _logger.LogInformation("5. OnStopped has been called.");
    }
}
```

The Worker Service template could be modified to add the `ExampleHostedService` implementation:

```cs
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using AppLifetime.Example;

using IHost host = Host.CreateDefaultBuilder(args)
    .ConfigureServices((_, services) =>
        services.AddHostedService<ExampleHostedService>())
    .Build();

await host.RunAsync();
```

The application would write the following sample output:

```cs
// Sample output:
//     info: ExampleHostedService[0]
//           1. StartAsync has been called.
//     info: ExampleHostedService[0]
//           2. OnStarted has been called.
//     info: Microsoft.Hosting.Lifetime[0]
//           Application started.Press Ctrl+C to shut down.
//     info: Microsoft.Hosting.Lifetime[0]
//           Hosting environment: Production
//     info: Microsoft.Hosting.Lifetime[0]
//           Content root path: ..\app-lifetime\bin\Debug\net6.0
//     info: ExampleHostedService[0]
//           3. OnStopping has been called.
//     info: Microsoft.Hosting.Lifetime[0]
//           Application is shutting down...
//     info: ExampleHostedService[0]
//           4. StopAsync has been called.
//     info: ExampleHostedService[0]
//           5. OnStopped has been called.
```

## [](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#ihostlifetime)IHostLifetime

The [IHostLifetime](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihostlifetime) implementation controls when the host starts and when it stops. The last implementation registered is used. `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation. For more information on the lifetime mechanics of shutdown, see [Host shutdown](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#host-shutdown).

## [](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#ihostenvironment)IHostEnvironment

Inject the [IHostEnvironment](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihostenvironment) service into a class to get information about the following settings:

-   [IHostEnvironment.ApplicationName](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihostenvironment.applicationname#microsoft-extensions-hosting-ihostenvironment-applicationname)
-   [IHostEnvironment.ContentRootFileProvider](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihostenvironment.contentrootfileprovider#microsoft-extensions-hosting-ihostenvironment-contentrootfileprovider)
-   [IHostEnvironment.ContentRootPath](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihostenvironment.contentrootpath#microsoft-extensions-hosting-ihostenvironment-contentrootpath)
-   [IHostEnvironment.EnvironmentName](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihostenvironment.environmentname#microsoft-extensions-hosting-ihostenvironment-environmentname)

## [](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#host-configuration)Host configuration

Host configuration is used to configure properties of the [IHostEnvironment](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#ihostenvironment) implementation.

The host configuration is available in [HostBuilderContext.Configuration](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostbuildercontext.configuration#microsoft-extensions-hosting-hostbuildercontext-configuration) within the [ConfigureAppConfiguration](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration) method. When calling the `ConfigureAppConfiguration` method, the `HostBuilderContext` and `IConfigurationBuilder` are passed into the `configureDelegate`. The `configureDelegate` is defined as an `Action<HostBuilderContext, IConfigurationBuilder>`. The host builder context exposes the `Configuration` property, which is an instance of `IConfiguration`. It represents the configuration built from the host, whereas the `IConfigurationBuilder` is the builder object used to configure the app.
 
> After `ConfigureAppConfiguration` is called the `HostBuilderContext.Configuration` is replaced with the [app config](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#app-configuration).

To add host configuration, call [ConfigureHostConfiguration](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostbuilder.configurehostconfiguration) on `IHostBuilder`. `ConfigureHostConfiguration` can be called multiple times with additive results. The host uses whichever option sets a value last on a given key.

The following example creates host configuration:

```cs
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;

using IHost host = Host.CreateDefaultBuilder(args)
    .ConfigureHostConfiguration(configHost =>
    {
        configHost.SetBasePath(Directory.GetCurrentDirectory());
        configHost.AddJsonFile("hostsettings.json", optional: true);
        configHost.AddEnvironmentVariables(prefix: "PREFIX_");
        configHost.AddCommandLine(args);
    })
    .Build();

// Application code should start here.

await host.RunAsync();
```

## [](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#app-configuration)App configuration

App configuration is created by calling [ConfigureAppConfiguration](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration) on `IHostBuilder`. `ConfigureAppConfiguration` can be called multiple times with additive results. The app uses whichever option sets a value last on a given key.

The configuration created by `ConfigureAppConfiguration` is available in [HostBuilderContext.Configuration](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostbuildercontext.configuration) for subsequent operations and as a service from DI. The host configuration is also added to the app configuration.

For more information, see [Configuration in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration).

## [](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#host-shutdown)Host shutdown

A hosted service process can be stopped in the following ways:

-   If someone doesn't call [Run](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostingabstractionshostextensions.run) or [HostingAbstractionsHostExtensions.WaitForShutdown](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostingabstractionshostextensions.waitforshutdown) and the app exits normally with `Main` completing.
-   If the app crashes.
-   If the app is forcefully shut down using [SIGKILL](https://en.wikipedia.org/wiki/Signal_(IPC)#SIGKILL) (or CTRL+Z).

All of these scenarios aren't handled directly by the hosting code. The owner of the process needs to deal with them the same as any application. There are several additional ways in which a hosted service process can be stopped:

-   If `ConsoleLifetime` is used, it listens for the following signals and attempts to stop the host gracefully.
    -   [SIGINT](https://en.wikipedia.org/wiki/Signal_(IPC)#SIGINT) (or CTRL+C).
    -   [SIGQUIT](https://en.wikipedia.org/wiki/Signal_(IPC)#SIGQUIT) (or CTRL+BREAK on Windows, CTRL+\ on Unix).
    -   [SIGTERM](https://en.wikipedia.org/wiki/Signal_(IPC)#SIGTERM) (sent by other apps, such as `docker stop`).
-   If the app calls [Environment.Exit](https://learn.microsoft.com/en-us/dotnet/api/system.environment.exit).

These scenarios are handled by the built-in hosting logic, specifically the `ConsoleLifetime` class. `ConsoleLifetime` tries to handle the "shutdown" signals SIGINT, SIGQUIT, and SIGTERM to allow for a graceful exit to the application.

==Before .NET 6, there wasn't a way for .NET code to gracefully handle `SIGTERM`.== To work around this limitation, `ConsoleLifetime` would subscribe to [System.AppDomain.ProcessExit](https://learn.microsoft.com/en-us/dotnet/api/system.appdomain.processexit). When `ProcessExit` was raised, `ConsoleLifetime` would signal the host to stop and block the `ProcessExit` thread, waiting for the host to stop. This would allow for the clean-up code in the application to run — for example, [IHost.StopAsync](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihost.stopasync) and code after [HostingAbstractionsHostExtensions.Run](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.hostingabstractionshostextensions.run) in the `Main` method.

This caused other issues because `SIGTERM` wasn't the only way `ProcessExit` was raised. It is also raised by code in the application calling `Environment.Exit`. `Environment.Exit` isn't a graceful way of shutting down a process in the `Microsoft.Extensions.Hosting` app model. It raises the `ProcessExit` event and then exits the process. The end of the `Main` method doesn't get executed. Background and foreground threads are terminated, and `finally` blocks are _not_ executed.

Since `ConsoleLifetime` blocked `ProcessExit` while waiting for the host to shut down, this behavior led to [deadlocks](https://github.com/dotnet/runtime/issues/50397) from `Environment.Exit` also blocks waiting for the call to `ProcessExit`. Additionally, since the SIGTERM handling was attempting to gracefully shut down the process, `ConsoleLifetime` would set the [ExitCode](https://learn.microsoft.com/en-us/dotnet/api/system.environment.exitcode#system-environment-exitcode) to `0`, which [clobbered](https://github.com/dotnet/runtime/issues/42224) the user's exit code passed to `Environment.Exit`.

==In .NET 6,== [POSIX signals](https://github.com/dotnet/runtime/issues/50527) are supported and handled. This allows for `ConsoleLifetime` to handle `SIGTERM` gracefully, and no longer get involved when `Environment.Exit` is invoked.
 
> For .NET 6+, `ConsoleLifetime` no longer has logic to handle scenario `Environment.Exit`. Apps that call `Environment.Exit` and need to perform clean-up logic can subscribe to `ProcessExit` themselves. Hosting will no longer attempt to gracefully stop the host in this scenario.

If your application uses hosting, and you want to gracefully stop the host, you can call [IHostApplicationLifetime.StopApplication](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.stopapplication) instead of `Environment.Exit`.

### [](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#hosting-shutdown-process)Hosting shutdown process

The following sequence diagram shows how the signals are handled internally in the hosting code. Most users don't need to understand this process. But for developers that need a deep understanding, this may help you get started.


After the host has been started, when a user calls `Run` or `WaitForShutdown`, a handler gets registered for [IApplicationLifetime.ApplicationStopping](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping). Execution is paused in `WaitForShutdown`, waiting for the `ApplicationStopping` event to be raised. This is how the `Main` method doesn't return right away, and the app stays running until `Run` or `WaitForShutdown` returns.

When a signal is sent to the process, it initiates the following sequence:

![[zIMG-NET-Core-Hosting-Shutdown.png]]

1.  The control flows from `ConsoleLifetime` to `ApplicationLifetime` to raise the `ApplicationStopping` event. This signals `WaitForShutdownAsync` to unblock the `Main` execution code. In the meantime, the POSIX signal handler returns with `Cancel = true` since this POSIX signal has been handled.
2.  The `Main` execution code starts executing again and tells the host to `StopAsync()`, which in turn stops all the hosted services, and raises any other stopped events.
3.  Finally, `WaitForShutdown` exits, allowing for any application clean up code to execute, and for the `Main` method to exit gracefully.

## [](https://learn.microsoft.com/en-us/dotnet/core/extensions/generic-host#see-also)See also

-   [Dependency injection in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection)
-   [Logging in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/logging)
-   [Configuration in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration)
-   [Worker Services in .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/workers)
-   [ASP.NET Core Web Host](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/host/web-host)
-   Generic host bugs should be created in the [github.com/dotnet/runtime](https://github.com/dotnet/runtime/issues) repo