#ASP_NET_CORE/Platform/Sessions

---

The example in the previous section used cookies to store the application’s state data, providing the middleware component with the data required. The problem with this approach is that the contents of the cookie are stored at the client, where it can be manipulated and used to alter the behavior of the application.
A better approach is to use the ASP.NET Core session feature. The session middleware adds a cookie to responses, which allows related requests to be identified and which is also associated with data stored at the server. When a request containing the session cookie is received, the session middleware component retrieves the server-side data associated with the session and makes it available to other middleware components through the `HttpContext` object. Using sessions means that the application’s data remains at the server and only the identifier for the session is sent to the browser.

## Configuring the Session Service and Middleware

Setting up sessions requires configuring services and adding a middleware component to the request pipeline. Listing 16-7 adds the statements to the Program.cs file to set up sessions for the example application and removes the endpoints from the previous section.
```cs
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddDistributedMemoryCache();
builder.Services.AddSession(opts => 
{
	opts.IdleTimeout = TimeSpan.FromMinutes(30);
	opts.Cookie.IsEssential = true;
});

var app = builder.Build();
app.UseSession();
app.UseMiddleware<Platform.ConsentMiddleware>();

app.MapFallback(async context => await context.Response.WriteAsync("Hello World!"));

app.Run();
```

When you use sessions, you must decide how to store the associated data. 
ASP.NET Core provides three options for session data storage, each of which has its own method to register a service, as described in Table 16-5.

Name|Description
--|--
AddDistributedMemoryCache|This method sets up an in-memory cache. **Despite the name, the cache is not distributed** and is responsible only for storing data for the instance of the ASP.NET Core runtime where it is created.
AddDistributedSqlServerCache|This method sets up a cache that stores data in SQL Server and is available when the `Microsoft.Extensions.Caching.SqlServer` package is installed. This cache is used in Chapter 17.
AddStackExchangeRedisCache|This method sets up a Redis cache and is available when the `Microsoft.Extensions.Caching.Redis` package is installed.

Despite its name, the cache service created by the `AddDistributedMemoryCache` method isn’t distributed and stores the session data for a single instance of the ASP.NET Core runtime. If you scale an application by deploying multiple instances of the runtime, then you should use one of the other caches, such as the SQL Server cache, which is demonstrated in Chapter 17.

Table 16-6 shows that the options class for sessions is `SessionOptions` and describes the key properties it defines. ^c98984

Name|Description
--|--
Cookie|This property is used to configure the session cookie.
IdleTimeout|This property is used to configure the time span after which a session expires.

The `Cookie` property returns an object that can be used to configure the session cookie.
Table 16-7 describes the most useful cookie configuration properties for session data.

Name|Description
--|--
HttpOnly|This property specifies whether the browser will prevent the cookie from being included in HTTP requests sent by JavaScript code. This property should be set to `true` for projects that use a JavaScript application whose requests should be included in the session. The default value is `true`.
IsEssential|This property specifies whether the cookie is required for the application to function and should be used even when the user has specified that they don’t want the application to use cookies. The default value is `false`. See the “Managing Cookie Consent” section for more details.
SecurityPolicy|This property sets the security policy for the cookie, using a value from the `CookieSecurePolicy` enum. The values are `Always` (which restricts the cookie to HTTPS requests), `SameAsRequest` (which restricts the cookie to HTTPS if the original request was made using HTTPS), and `None` (which allows the cookie to be used on HTTP and HTTPS requests). The default value is `None`.

The options set in Listing 16-7 allow the session cookie to be included in requests started by JavaScript and flag the cookie as essential so that it will be used even when the user has expressed a preference not to use cookies (see the “Managing Cookie Consent” section for more details about essential cookies). 
The `IdleTimeout` option has been set so that sessions expire if no request containing the sessions cookie is received for 30 minutes.

> ![[zICO - Warning - 16.png]] The session cookie isn’t denoted as essential by default, which can cause problems when cookie consent is used. Listing 16-7 sets the `IsEssential` property to `true` to ensure that sessions always work. 
> If you find sessions don’t work as expected, then this is the likely cause, and you must either set `IsEssentia`l to `true` or adapt your application to deal with users who don’t grant consent and won’t accept session cookies.

The final step is to add the session middleware component to the request pipeline, which is done with the `UseSession` method. When the middleware processes a request that contains a session cookie, it retrieves the session data from the cache and makes it available through the `HttpContext` object, before passing the request along the request pipeline and providing it to other middleware components. 

> ![[zICO - Warning - 16.png]] **When a request arrives without a session cookie, a new session is started**, and a cookie is added to the response so that subsequent requests can be identified as being part of the session.

## Using Session Data

The session middleware provides access to details of the session associated with a request through the `Session` property of the `HttpContext` object. The `Session` property returns an object that implements the `ISession` interface, which provides the methods shown in Table 16-8 for accessing session data.

Name|Description
--|--
Clear()|This method removes all the data in the session.
CommitAsync()|This asynchronous method commits changed session data to the cache.
GetString(key)|This method retrieves a string value using the specified key.
GetInt32(key)|This method retrieves an integer value using the specified key.
Id|This property returns the unique identifier for the session.
IsAvailable|This returns true when the session data has been loaded.
Keys|This enumerates the keys for the session data items.
Remove(key)|This method removes the value associated with the specified key.
SetString(key, val)|This method stores a string using the specified key.
SetInt32(key, val)|This method stores an integer using the specified key.

Session data is stored in key-value pairs, where the keys are strings and the values are strings or integers. This simple data structure allows session data to be stored easily by each of the caches listed in [[#^c98984|Table 16-6]] .
Applications that need to store more complex data can use serialization, which is the approach I took for the SportsStore. 

Listing 16-8 uses session data to re-create the counter example.
```cs
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddDistributedMemoryCache();
builder.Services.AddSession(opts => 
{
	opts.IdleTimeout = TimeSpan.FromMinutes(30);
	opts.Cookie.IsEssential = true;
});

var app = builder.Build();
app.UseSession();
app.UseMiddleware<Platform.ConsentMiddleware>();

app.MapGet("/session", async context => 
{
	int counter1 = (context.Session.GetInt32("counter1") ?? 0) + 1;
	int counter2 = (context.Session.GetInt32("counter2") ?? 0) + 1;
	context.Session.SetInt32("counter1", counter1);
	context.Session.SetInt32("counter2", counter2);
	await context.Session.CommitAsync();
	await context.Response.WriteAsync($"Counter1: {counter1}, Counter2: {counter2}");
});

app.MapFallback(async context => await context.Response.WriteAsync("Hello World!"));

app.Run();
```

The `GetInt32` method is used to read the values associated with the keys counter1 and counter2. **If this is the first request in a session, no value will be available, and the null-coalescing operator is used to provide an initial value**. The value is incremented and then stored using the `SetInt32` method and used to generate a simple result for the client.

> The use of the `CommitAsync` method is optional, but it is good practice to use it because it will throw an exception if the session data can’t be stored in the cache. By default, no error is reported if there are caching problems, which can lead to unpredictable and confusing behavior.

> ![[zICO - Warning - 16.png]] All changes to the session data must be made before the response is sent to the client, which is why I read, update, and store the session data before calling the `Response.WriteAsync` method in Listing 16-8.

> ![[zICO - Warning - 16.png]] Notice that the statements in Listing 16-8 do not have to deal with the session cookie, detect expired sessions, or load the session data from the cache. All this work is done automatically by the session middleware, which presents the results through the `HttpContext.Session` property. One consequence of this approach is that the `HttpContext.Session` property is not populated with data until after the session middleware has processed a request, which means that you should attempt to access session data only in middleware or endpoints that are added to the request pipeline after the `UseSession` method is called.

The sessions and session data will be lost when ASP.NET Core is stopped because I chose the in-memory cache. The other storage options operate outside of the ASP.NET Core runtime and survive application restarts.
