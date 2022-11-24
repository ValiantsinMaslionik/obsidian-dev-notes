#ASP_NET_CORE/Platform/Routing

---

## Matching Multiple Values from a Single URL Segment

Most segment variables correspond directly to a segment in the URL path, but the routing middleware
is able to perform more complex matches, allowing a single segment to be matched to a variable while
discarding unwanted characters. Listing 13-15 defines a route that matches only part of a URL segment to a variable.

Listing 13-15 ^fa68d8

```cs
using Platform;

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("files/{filename}.{ext}", async context => 
{
	await context.Response.WriteAsync("Request Was Routed\n");
	foreach (var kvp in context.Request.RouteValues) 
	{
		await context.Response.WriteAsync($"{kvp.Key}: {kvp.Value}\n");
	}
});

app.MapGet("capital/{country}", Capital.Endpoint);
app.MapGet("size/{city}", Population.Endpoint).WithMetadata(new RouteNameMetadata("population"));
app.Run();
```

A URL pattern can contain as many segment variables as you need, as long as they are separated by a
static string. ==The requirement for a static separator is so the routing middleware knows where the content for
one variable ends and the content for the next starts.== The pattern in [[#^fa68d8|Listing 13-15]] matches segment variables
named filename and ext, which are separated by a period; this pattern is often used by process file names.

### Avoiding the complex pattern mismatching pitfall

![[zICO - Exclamation - 16.png]] 

At the time of writing, there is a problem with URL patterns where the content that should be matched 
by the first variable also appears as a literal string at the start of a segment. This is easier to understand 
with an example, as shown here:

```cs
...
app.MapGet("example/red{color}", async context => {
...
```

This pattern has a segment that begins with the literal string <b><span style="color:red">red</span></b>, followed by a segment variable
named <b>color</b>. The routing middleware will correctly match the pattern against the URL path 
<b>example/<span style="color:red">red</span><span style="color:green">green</span></b>, and the value of the color route variable will be <b><span style="color:green">green</span></b>. However, the URL path 
<b>example/<span style="color:red">red</span><span style="color:red">red</span><span style="color:green">green</span></b> won’t match because the matching process confuses the position of the literal content
with the first part of the content that should be assigned to the <b>color</b> variable. This problem may be
fixed by the time you read this book, but there will be other issues with complex patterns. 

> ![[zICO - Warning - 16.png]] It is a good idea to keep URL patterns as simple as possible and make sure you get the matching results you expect.

## Using Default Values for Segment Variables

Patterns can be defined with default values that are used when the URL doesn’t contain a value for the
corresponding segment, increasing the range of URLs that a route can match. 

```cs
using Platform;
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.MapGet("files/{filename}.{ext}", async context => 
{
	await context.Response.WriteAsync("Request Was Routed\n");
	foreach (var kvp in context.Request.RouteValues) 
	{
		await context.Response.WriteAsync($"{kvp.Key}: {kvp.Value}\n");
	}
});

app.MapGet("capital/{country=France}", Capital.Endpoint);
app.MapGet("size/{city}", Population.Endpoint).WithMetadata(new RouteNameMetadata("population"));
app.Run();
```

Default values are defined using an equal sign and the value to be used. The default value in the listing
uses the value France when there is no second segment in the URL path. 

URL|Path Description
--|--
/|No match—too few segments
/city|No match—first segment isn’t capital
/capital|Matches, country variable is France
/capital/uk|Matches, country variable is uk
/capital/europe/italy|No match—too many segments

## Using Optional Segments in a URL Pattern

Default values allow URLs to be matched with fewer segments, but the use of the default value isn’t obvious
to the endpoint. Some endpoints define their own responses to deal with URLs that omit segments, for
which optional segments are used. 

```cs
using Platform;

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.MapGet("files/{filename}.{ext}", async context => 
{
	await context.Response.WriteAsync("Request Was Routed\n");
	foreach (var kvp in context.Request.RouteValues) 
	{
		await context.Response.WriteAsync($"{kvp.Key}: {kvp.Value}\n");
	}
});

app.MapGet("capital/{country=France}", Capital.Endpoint);
app.MapGet("size/{city?}", Population.Endpoint).WithMetadata(new RouteNameMetadata("population"));
app.Run();
```

Optional segments are denoted with a question mark (the _?_ character) after the variable name and
allow the route to match URLs that don’t have a corresponding path segment, as described in Table 13-7.

URL|Path Description
--|--
/|No match—too few segments.
/city|No match—first segment isn’t size.
/size|Matches. No value for the city variable is provided to the endpoint.
/size/paris|Matches, city variable is paris.
/size/europe/italy|No match—too many segments.

## Using a catchall Segment Variable

Optional segments allow a pattern to match shorter URL paths. A catchall segment does the opposite and
allows routes to match URLs that contain more segments than the pattern. A catchall segment is denoted
with an asterisk (_*_) before the variable name, as shown in Listing 13-19.

```cs
using Platform;

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.MapGet("{first}/{second}/{*catchall}", async context => 
{
	await context.Response.WriteAsync("Request Was Routed\n");
	foreach (var kvp in context.Request.RouteValues) 
	{
		await context.Response.WriteAsync($"{kvp.Key}: {kvp.Value}\n");
	}
});
app.MapGet("capital/{country=France}", Capital.Endpoint);
app.MapGet("size/{city?}", Population.Endpoint).WithMetadata(new RouteNameMetadata("population"));
app.Run();
```

The new pattern contains two-segment variables and a catchall, and the result is that the route
will match any URL whose path contains two or more segments. There is no upper limit to the number
of segments that the URL pattern in this route will match, and the contents of any additional segments
are assigned to the segment variable named catchall. 

> Notice that the segments captured by the catchall are presented in the form segment/segment/segment
> and that the endpoint is responsible for processing the string to break out the individual segments.

## Constraining Segment Matching

Default values, optional segments, and catchall segments all increase the range of URLs that a route will
match. Constraints have the opposite effect and restrict matches. This can be useful if an endpoint can deal
only with specific segment contents or if you want to differentiate matching closely related URLs for different
endpoints. Constraints are applied by a colon (the _:_ character) and a constraint type after a segment variable
name

```cs
using Platform;

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.MapGet("{first:int}/{second:bool}", async context => 
{
	await context.Response.WriteAsync("Request Was Routed\n");
	foreach (var kvp in context.Request.RouteValues) 
	{
		await context.Response.WriteAsync($"{kvp.Key}: {kvp.Value}\n");
	}
});
app.MapGet("capital/{country=France}", Capital.Endpoint);
app.MapGet("size/{city?}", Population.Endpoint).WithMetadata(new RouteNameMetadata("population"));
app.Run();
```

This example constrains the first segment variable so it will match only the path segments that can be
parsed to an int value, and it constrains the second segment so it will match only the path segments that
can be parsed to a bool. Values that don’t match the constraints won’t be matched by the route.

> Some of the constraints match types whose format can differ based on locale. The routing
> middleware doesn’t handle localized formats and will match only those values that are expressed in the
> invariant culture format.

Table 13-8 ^8dad68

Constraint|Description
--|--
alpha|This constraint matches the letters a to z (and is case-insensitive).
bool|This constraint matches true and false (and is case-insensitive).
datetime|This constraint matches DateTime values, expressed in the nonlocalized invariant culture format.
decimal|This constraint matches decimal values, formatted in the nonlocalized invariant culture.
double|This constraint matches double values, formatted in the nonlocalized invariant culture.
file|This constraint matches segments whose content represents a file name, in the form name.ext. The existence of the file is not validated.
float|This constraint matches float values, formatted in the nonlocalized invariant culture.
guid|This constraint matches GUID values.
int|This constraint matches int values.
length(len)|This constraint matches path segments that have the specified number of characters.
length(min, max)|This constraint matches path segments whose length falls between the lower and upper values specified.
long|This constraint matches long values.
max(val)|This constraint matches path segments that can be parsed to an int value that is less than or equal to the specified value.
maxlength(len)|This constraint matches path segments whose length is equal to or less than the specified value.
min(val)|This constraint matches path segments that can be parsed to an int value that is more than or equal to the specified value.
minlength(len)|This constraint matches path segments whose length is equal to or more than the specified value.
nonfile|This constraint matches segments that do not represent a file name, i.e., values that would not be matched by the file constraint.
range(min, max)|This constraint matches path segments that can be parsed to an int value that falls between the inclusive range specified.
regex(expression)|This constraint applies a regular expression to match path segments.

Constraints can be combined to further restrict matching, as shown in Listing 13-21.

```cs
using Platform;
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.MapGet("{first:alpha:length(3)}/{second:bool}", async context => 
{
	await context.Response.WriteAsync("Request Was Routed\n");
	foreach (var kvp in context.Request.RouteValues) 
	{
		await context.Response.WriteAsync($"{kvp.Key}: {kvp.Value}\n");
	}
});
app.MapGet("capital/{country=France}", Capital.Endpoint);
app.MapGet("size/{city?}", Population.Endpoint).WithMetadata(new RouteNameMetadata("population"));
app.Run();
```

The constraints are combined, and only path segments that can satisfy all the constraints will be matched.

### Constraining Matching to a Specific Set of Values

The regex constraint applies a regular expression, which provides the basis for one of the most commonly
required restrictions: matching only a specific set of values. In Listing 13-22, I have applied the regex
constraint to the routes for the Capital endpoint, so it will receive requests only for the values it can process.

```cs
using Platform;
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.MapGet("{first:alpha:length(3)}/{second:bool}", async context => 
{
	await context.Response.WriteAsync("Request Was Routed\n");
	foreach (var kvp in context.Request.RouteValues) 
	{
		await context.Response.WriteAsync($"{kvp.Key}: {kvp.Value}\n");
	}
});
app.MapGet("capital/{country:regex(^uk|france|monaco$)}", Capital.Endpoint);
app.MapGet("size/{city?}", Population.Endpoint).WithMetadata(new RouteNameMetadata("population"));
app.Run();
```

The route will match only those URLs with two segments. The first segment must be **capital**, and
the second segment must be **uk**, **france**, or **monaco**. 

>![[zICO - Warning - 16.png]] Regular expressions are case-insensitive