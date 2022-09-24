#ASP_NET_CORE/Platform 

---

The HttpContext object describes the HTTP request and the HTTP response and provides additional
context, including details of the user associated with the request. Table 12-4 describes the most useful
members provided by the HttpContext class, which is defined in the _Microsoft.AspNetCore.Http_ namespace.

Name|Description
--|--
Connection|This property returns a ConnectionInfo object that provides information about the network connection underlying <br> the HTTP request, including details of local and remote IP addresses and ports.
Request|This property returns an [[ASP.NET Core - HttpRequest]] object that describes the HTTP request being processed.
RequestServices|This property provides access to the services available for the request
Response|This property returns an [[ASP.NET Core - HttpResponse]] object that is used to create a response to the HTTP request.
Session|This property returns the session data associated with the request.
User|This property returns details of the user associated with the request
Features|This property provides access to request features, which allow access to the low-level aspects of request handling.ture.
