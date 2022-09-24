#ASP_NET_CORE/Platform

---

[[#Understanding URL Routing]]
[[#Adding the Routing Middleware and Defining an Endpoint]]

## Understanding URL Routing

Each middleware component decides whether to act on a request as it passes along the pipeline. Some
components are looking for a specific header or query string value, but most components—especially
terminal and short-circuiting components—are trying to match URLs.

Each middleware component has to repeat the same set of steps as the request works its way along the
pipeline. You can see this in the middleware defined in the previous section, where both components go
through the same process: split up the URL, check the number of parts, inspect the first part, and so on.

This approach is far from ideal. It is inefficient because the same set of operations is repeated to process
the URL. It is difficult to maintain because the URL that each component is looking for is hidden in its code.
It breaks easily because changes must be carefully worked through in multiple places. For example, the
Capital component redirects requests to a URL whose path starts with /population, which is handled by
the Population component. If the Population component is revised to support the /size URL instead, then
this change must also be reflected in the Capital component. Real applications can support complex sets of
URLs and working changes fully through individual middleware components can be difficult.

URL routing solves these problems by introducing middleware that takes care of matching request
URLs so that components, called endpoints, can focus on responses. The mapping between endpoints and
the URLs they require is expressed in a route. The routing middleware processes the URL, inspects the set of
routes, and finds the endpoint to handle the request, a process known as #Term/routing.

## Adding the Routing Middleware and Defining an Endpoint

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

>The routing middleware **short-circuits** the pipeline when a route matches a URL so that the response is generated only by the route’s endpoint. 
>The request isn’t forwarded to other endpoints or middleware components that appear later in the request pipeline.
>If the request URL isn’t matched by any route, then the routing middleware passes the request to the next middleware component in the request pipeline.
