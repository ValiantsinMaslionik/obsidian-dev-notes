#aspnet_core/http2

---

# How do I enable http2 in C# Kestrel web server over plain http?

[StackOverflow](https://stackoverflow.com/questions/58682933/how-do-i-enable-http2-in-c-sharp-kestrel-web-server-over-plain-http)

**Q:** How do I (and is it possible) to enable http2 over plain http in the C# Kestrel web server? All Microsoft documentation indicates that https/TLS is required, but I have services that will be running behind a load-balancer or nginx and as such don't need a second layer of https. The official http2 spec indicates that https is not required.

**A:** Unencrypted http2 can be necessary for load balancers, proxies, etc.

You must do three things to use http2 over unencrypted channel.

1) Setup Kestrel to use HTTP2 on your server:
```cs
builder.ConfigureWebHostDefaults((webBuilder) =>
{
    // this will keep your other end points settings such as --urls parameter
    webBuilder.ConfigureKestrel((options) =>
    {
        // trying to use Http1AndHttp2 causes http2 connections to fail with invalid protocol error
        // according to Microsoft dual http version mode not supported in unencrypted scenario: https://learn.microsoft.com/en-us/aspnet/core/grpc/troubleshoot?view=aspnetcore-3.0
        options.ConfigureEndpointDefaults(lo => lo.Protocols = HttpProtocols.Http2);
    });
});
```

2) For .net 5+, create your `HttpClient` instance, then create a message and specify the version:
```cs
var request = new HttpRequestMessage(HttpMethod.Get, uri)
{
    Version = HttpVersion.Version20,
    VersionPolicy = HttpVersionPolicy.RequestVersionOrHigher
};
```

3) For .net core 3.1 and older, set a flag to enable http2 unencrypted. Then, when you create an `HttpClient`, specify the version:
```cs
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);
var client = new HttpClient { BaseAddress = new Uri(baseUrl), DefaultRequestVersion = new Version(2, 0) };
```

> If you need to support both HTTP1 and HTTP2 on a completely unencrypted host, then you will need to listen on two ports, one for each HTTP version. Then your load balancer or proxy would need to handle the HTTP version and direct to the appropriate port. You won't see HTTP2 on your browser and will likely get a protocol error, so in those cases you can use an HTTP1 protocol directive just for development environment. Not ideal, but it at least lets you test locally.