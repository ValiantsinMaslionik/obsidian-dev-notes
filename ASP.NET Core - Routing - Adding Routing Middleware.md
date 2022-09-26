#ASP_NET_CORE/Platform/Routing

---

The routing middleware is added using two separate methods: _UseRouting_ and _UseEndpoints_. 
- The UseRouting method adds the middleware responsible for processing requests to the pipeline. 
- The UseEndpoints method is used to define the routes that match URLs to endpoints. 

There are no arguments to the UseRouting method. The UseEndpoints method receives a function
that accepts an IEndpointRouteBuilder object and uses it to create routes using the extension methods

URLs are matched using patterns that are compared to the path of request URLs, and each route creates
a relationship between one URL pattern and one endpoint. 

Name|Description
--|--
MapGet(pattern, endpoint)|This method routes HTTP GET requests that match the URL pattern to the endpoint.
MapPost(pattern, endpoint)|This method routes HTTP POST requests that match the URL pattern to the endpoint.
MapPut(pattern, endpoint)|This method routes HTTP PUT requests that match the URL pattern to the endpoint.
MapDelete(pattern, endpoint)|This method routes HTTP DELETE requests that match the URL pattern to the endpoint.
MapMethods(pattern, methods, endpoint)|This method routes requests made with one of the specified HTTP methods that match the URL pattern to the endpoint.
Map(pattern, endpoint)|This method routes all HTTP requests that match the URL pattern to the endpoint.

> [[ASP.NET Core - Endpoints|Endpoints]] are defined using RequestDelegate, which is the same delegate used by conventional
> middleware, so endpoints are asynchronous methods that receive an HttpContext object and use it to generate a response.

>![[zICO - Exclamation - 16.png]] The routing middleware **short-circuits** the pipeline when a route matches a URL so that the response is generated only by the route’s endpoint. 
>The request isn’t forwarded to other endpoints or middleware components that appear later in the request pipeline.
>If the request URL isn’t matched by any route, then the routing middleware passes the request to the next middleware component in the request pipeline.

>![[zICO - Warning - 16.png]] As part of a drive to simplify the configuration of ASP.NET Core applications, Microsoft
> automatically applies the UseRouting and UseEndpoints methods to the request pipeline
