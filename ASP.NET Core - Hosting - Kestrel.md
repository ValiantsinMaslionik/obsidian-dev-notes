#aspnet_core/hosting 

---

# [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-7.0)Overview
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-7.0#get-started)Get started
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-7.0#optional-client-certificates)Optional client certificates
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-7.0#additional-resources)Additional resources
-   [Configure endpoints for the ASP.NET Core Kestrel web server](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0)
-   Source for [`WebApplication.CreateBuilder` method call to `UseKestrel`](https://github.com/dotnet/aspnetcore/blob/v6.0.2/src/DefaultBuilder/src/WebHost.cs#L224)
-   [Configure options for the ASP.NET Core Kestrel web server](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0)
-   [Use HTTP/2 with the ASP.NET Core Kestrel web server](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http2?view=aspnetcore-7.0)
-   [When to use a reverse proxy with the ASP.NET Core Kestrel web server](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/when-to-use-a-reverse-proxy?view=aspnetcore-7.0)
-   [Host filtering with ASP.NET Core Kestrel web server](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/host-filtering?view=aspnetcore-7.0)
-   [Troubleshoot and debug ASP.NET Core projects](https://learn.microsoft.com/en-us/aspnet/core/test/troubleshoot?view=aspnetcore-7.0)
-   [Enforce HTTPS in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/enforcing-ssl?view=aspnetcore-7.0)
-   [Configure ASP.NET Core to work with proxy servers and load balancers](https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/proxy-load-balancer?view=aspnetcore-7.0)
-   [RFC 9110: HTTP Semantics (Section 7.2: Host and :authority)](https://www.rfc-editor.org/rfc/rfc9110#field.host)
-   When using UNIX sockets on Linux, the socket isn't automatically deleted on app shutdown. For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).
&nbsp;
&nbsp;
# [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0)Configure endpoints for the ASP.NET Core Kestrel web server
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#configureendpointdefaultsactionlistenoptions)`ConfigureEndpointDefaults(Action<ListenOptions>)`
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#configureiconfiguration)`Configure(IConfiguration)`
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#configurehttpsdefaultsactionhttpsconnectionadapteroptions)`ConfigureHttpsDefaults(Action<HttpsConnectionAdapterOptions>)`
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#listenoptionsusehttps)ListenOptions.UseHttps
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#no-configuration)No configuration
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#replace-the-default-certificate-from-configuration)Replace the default certificate from configuration
#### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#certificate-sources)Certificate sources
#### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#configurationloader)ConfigurationLoader
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#change-the-defaults-in-code)Change the defaults in code
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#configure-endpoints-using-server-name-indication)Configure endpoints using Server Name Indication
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#sni-with-servercertificateselector)SNI with `ServerCertificateSelector`
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#sni-with-serveroptionsselectioncallback)SNI with `ServerOptionsSelectionCallback`
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#sni-with-tlshandshakecallbackoptions)SNI with `TlsHandshakeCallbackOptions`
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#sni-in-configuration)SNI in configuration
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#sni-requirements)SNI requirements
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#ssltls-protocols)SSL/TLS Protocols
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#client-certificates)Client Certificates
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#connection-logging)Connection logging
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#bind-to-a-tcp-socket)Bind to a TCP socket
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#bind-to-a-unix-socket)Bind to a Unix socket
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#port-0)Port 0
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#limitations)Limitations
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#iis-endpoint-configuration)IIS endpoint configuration
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#listenoptionsprotocols)ListenOptions.Protocols
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#connection-middleware)Connection Middleware
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#set-the-http-protocol-from-configuration)Set the HTTP protocol from configuration
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-7.0#url-prefixes)URL prefixes
&nbsp;
# [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0)Configure options for the ASP.NET Core Kestrel web server
##  [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#general-limits)General limits
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#keep-alive-timeout)Keep-alive timeout
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#maximum-client-connections)Maximum client connections
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#maximum-request-body-size)Maximum request body size
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#request-headers-timeout)Request headers timeout
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#http2-limits)HTTP/2 limits
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#maximum-streams-per-connection)Maximum streams per connection
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#header-table-size)Header table size
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#maximum-frame-size)Maximum frame size
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#maximum-request-header-size)Maximum request header size
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#initial-connection-window-size)Initial connection window size
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#initial-stream-window-size)Initial stream window size
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#http2-keep-alive-ping-configuration)HTTP/2 keep alive ping configuration
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#other-options)Other options
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#synchronous-io)Synchronous I/O
&nbsp;
# [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0)Configure options for the ASP.NET Core Kestrel web server
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#general-limits)General limits
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#keep-alive-timeout)Keep-alive timeout
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#maximum-client-connections)Maximum client connections
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#maximum-request-body-size)Maximum request body size
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#minimum-request-body-data-rate)Minimum request body data rate
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#request-headers-timeout)Request headers timeout
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#http2-limits)HTTP/2 limits
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#maximum-streams-per-connection)Maximum streams per connection
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#header-table-size)Header table size
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#maximum-frame-size)Maximum frame size
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#maximum-request-header-size)Maximum request header size
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#initial-connection-window-size)Initial connection window size
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#initial-stream-window-size)Initial stream window size
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#http2-keep-alive-ping-configuration)HTTP/2 keep alive ping configuration
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#other-options)Other options
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/options?view=aspnetcore-7.0#synchronous-io)Synchronous I/O
&nbsp;
# [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/diagnostics?view=aspnetcore-7.0)Diagnostics in Kestrel
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/diagnostics?view=aspnetcore-7.0#logging)Logging
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/diagnostics?view=aspnetcore-7.0#connection-logging)Connection logging
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/diagnostics?view=aspnetcore-7.0#metrics)Metrics
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/diagnostics?view=aspnetcore-7.0#diagnosticsource)DiagnosticSource
&nbsp;
# [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http2?view=aspnetcore-7.0)Use HTTP/2 with the ASP.NET Core Kestrel web server
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http2?view=aspnetcore-7.0#advanced-http2-features-1)Advanced HTTP/2 features
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http2?view=aspnetcore-7.0#trailers-1)Trailers
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http2?view=aspnetcore-7.0#reset-1)Reset
&nbsp;
# [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http3?view=aspnetcore-7.0)Use HTTP/3 with the ASP.NET Core Kestrel web server
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http3?view=aspnetcore-7.0#http3-requirements)HTTP/3 requirements
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http3?view=aspnetcore-7.0#windows)Windows
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http3?view=aspnetcore-7.0#linux)Linux
### [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http3?view=aspnetcore-7.0#macos)macOS
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http3?view=aspnetcore-7.0#getting-started)Getting started
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http3?view=aspnetcore-7.0#alt-svc)Alt-svc
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http3?view=aspnetcore-7.0#localhost-testing)Localhost testing
## [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/http3?view=aspnetcore-7.0#http3-benefits)HTTP/3 benefits
&nbsp;
# [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/when-to-use-a-reverse-proxy?view=aspnetcore-7.0) When to use Kestrel with a reverse proxy
&nbsp;
# [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/host-filtering?view=aspnetcore-7.0)Host filtering with ASP.NET Core Kestrel web server
&nbsp;
# [](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel/request-draining?view=aspnetcore-7.0)Request draining with ASP.NET Core Kestrel web server