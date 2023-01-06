#aspnet_core/Routing 

---

## Creating Custom Constraints

If the constraints described in [[ASP.NET Core - Routing - Managing URL Matching#^8dad68|Table 13-8]] are not sufficient, you can define your own custom constraints
by implementing the [IRouteConstraint](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.routing.irouteconstraint?view=aspnetcore-6.0) interface. 
```cs
namespace Platform 
{
	public class CountryRouteConstraint : IRouteConstraint 
	{
		private static string[] countries = { "uk", "france", "monaco" };
		public bool Match(HttpContext? httpContext, IRouter? route, string routeKey, RouteValueDictionary values, RouteDirection routeDirection) 
		{
			string segmentValue = values[routeKey] as string ?? "";
			return Array.IndexOf(countries, segmentValue.ToLower()) > -1;
		}
	}
}
```
The Match method returns true if the constraint is satisfied by the request and false if it is not. 
The constraint in Listing 13-24 defines a set of countries that are compared to the value of the segment variable 
to which the constraint has been applied. The constraint is satisfied if the segment matches one of the countries. 
Custom constraints are set up using the options pattern, as shown in Listing 13-25.
```cs
using Platform;

var builder = WebApplication.CreateBuilder(args);
// Adding custom rote constraint
builder.Services.Configure<RouteOptions>(opts => 
{
	opts.ConstraintMap.Add("countryName", typeof(CountryRouteConstraint));
});
var app = builder.Build();
app.MapGet("capital/{country:countryName}", Capital.Endpoint);
app.MapGet("capital/{country:regex(^uk|france|monaco$)}", Capital.Endpoint);
app.MapGet("size/{city?}", Population.Endpoint).WithMetadata(new RouteNameMetadata("population"));
app.MapFallback(async context => 
{
	await context.Response.WriteAsync("Routed to fallback endpoint");
});
app.Run();
```
The options pattern is applied to the RouteOptions class, which defines the ConstraintMap property.
Each constraint is registered with a key that allows it to be applied in URL patterns. 

## Avoiding Ambiguous Route Exceptions

When trying to route a request, the routing middleware assigns each route a score. As explained earlier in the
chapter, precedence is given to more specific routes, and route selection is usually a straightforward process
that behaves predictably, albeit with the occasional surprise if you don’t think through and test the full range
of URLs the application will support.

If two routes have the same score, the routing system can’t choose between them and throws an
exception, indicating that the routes are ambiguous. In most cases, the best approach is to modify the
ambiguous routes to increase specificity by introducing literal segments or a constraint. There are some
situations where that won’t be possible, and some extra work is required to get the routing system to work
as intended.
```cs
using Platform;
var builder = WebApplication.CreateBuilder(args);
builder.Services.Configure<RouteOptions>(opts => 
{
	opts.ConstraintMap.Add("countryName", typeof(CountryRouteConstraint));
});
var app = builder.Build();
app.Map("{number:int}", async context => 
{
	await context.Response.WriteAsync("Routed to the int endpoint");
});
app.Map("{number:double}", async context => 
{
	await context.Response.WriteAsync("Routed to the double endpoint");
});
app.MapFallback(async context => 
{
	await context.Response.WriteAsync("Routed to fallback endpoint");
});
app.Run();
```
These routes are ambiguous only for some values. Only one route matches URLs where the first path
segment can be parsed to a double, but both routes match for where the segment can be parsed as an int
or a double.

For these situations, preference can be given to a route by defining its order relative to other matching
routes
```cs
using Platform;

var builder = WebApplication.CreateBuilder(args);
builder.Services.Configure<RouteOptions>(opts => 
{
	opts.ConstraintMap.Add("countryName", typeof(CountryRouteConstraint));
});

var app = builder.Build();
app.Map("{number:int}", async context => 
{
	await context.Response.WriteAsync("Routed to the int endpoint");
})
.Add(b => ((RouteEndpointBuilder)b).Order = 1);

app.Map("{number:double}", async context => 
{
	await context.Response.WriteAsync("Routed to the double endpoint");
})
.Add(b => ((RouteEndpointBuilder)b).Order = 2);

app.MapFallback(async context => 
{
	await context.Response.WriteAsync("Routed to fallback endpoint");
});

app.Run();
```
The process is awkward and requires a call to the Add method, casting to a RouteEndpointBuilder and
setting the value of the Order property. Precedence is given to the route with the lowest Order value, which
means that these changes tell the routing system to use the first route for URLs that both routes can handle.

## Accessing the Endpoint in a Middleware Component

As earlier chapters demonstrated, not all middleware generates responses. Some components provide
features used later in the request pipeline, such as the session middleware, or enhance the response in some
way, such as status code middleware.

>![[zICO - Warning - 16.png]] One limitation of the normal request pipeline is that a middleware component at the start of the
> pipeline can’t tell which of the later components will generate a response. The routing middleware does something different.

As I demonstrated at the start of the chapter, routing is set up by calling the UseRouting and
UseEndpoints methods, either explicitly or relying on the ASP.NET Core platform to call them during startup.
Although routes are registered in the UseEndpoints method, the selection of a route is done in the
UseRouting method, and the endpoint is executed to generate a response in the UseEndpoints method.

> Any middleware component that is added to the request pipeline between the UseRouting method and the
> UseEndpoints method can see which endpoint has been selected before the response is generated and alter
> its behavior accordingly.

In Listing 13-28, I have added a middleware component that adds different messages to the response
based on the route that has been selected to handle the request.
```cs
using Platform;

var builder = WebApplication.CreateBuilder(args);
builder.Services.Configure<RouteOptions>(opts => 
{
	opts.ConstraintMap.Add("countryName", typeof(CountryRouteConstraint));
});

var app = builder.Build();

// Use custom middleware to get the route that is serving current request
app.Use(async (context, next) => 
{
	Endpoint? end = context.GetEndpoint();
	if (end != null) 
	{
		await context.Response.WriteAsync($"{end.DisplayName} Selected \n");
	} 
	else 
	{
		await context.Response.WriteAsync("No Endpoint Selected \n");
	}
	await next();
});

app.Map("{number:int}", async context => 
{
	await context.Response.WriteAsync("Routed to the int endpoint");
})
.WithDisplayName("Int Endpoint")
.Add(b => ((RouteEndpointBuilder)b).Order = 1);

app.Map("{number:double}", async context => 
{
	await context.Response.WriteAsync("Routed to the double endpoint");
})
.WithDisplayName("Double Endpoint")
.Add(b => ((RouteEndpointBuilder)b).Order = 2);

app.MapFallback(async context => 
{
	await context.Response.WriteAsync("Routed to fallback endpoint");
});

app.Run();
```
The GetEndpoint extension method on the HttpContext class returns the endpoint that has been
selected to handle the request, described through an Endpoint object. The Endpoint class defines the
properties described in [[#^7d3f7a|Table 13-10]].

>![[zICO - Exclamation - 16.png]] There is also a SetEndpoint method that allows the endpoint chosen by the routing middleware
> to be changed before the response is generated. This should be used with caution and only when there is a
> compelling need to interfere with the normal route selection process.

To make it easier to identify the endpoint that the routing middleware has selected, I used the
WithDisplayName method to assign names to the routes in Listing 13-28.

Table 13-10 ^7d3f7a

Name|Description
--|--
DisplayName|This property returns the display name associated with the endpoint,<br>which can be set using the WithDisplayName method when creating a route.
Metadata|This property returns the collection of metadata associated with the endpoint.
RequestDelegate|This property returns the delegate that will be used to generate the response.
