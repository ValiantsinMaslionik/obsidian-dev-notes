#ASP_NET_CORE/Platform/Caching

---

In most web applications, there will be some items of data that are relatively expensive to generate but are required repeatedly. The exact nature of the data is specific to each project, but repeatedly performing the same set of calculations can increase the resources required to host the application. To represent an expensive response, add a class file called *SumEndpoint.cs* to the *Platform* folder with the code shown in Listing 17-3.

Listing 17-3. The Contents of the SumEndpoint.cs File in the Platform Folder
```cs
namespace Platform 
{
	public class SumEndpoint 
	{
		public async Task Endpoint(HttpContext context) 
		{
			int count;
			int.TryParse((string?)context.Request.RouteValues["count"], out count);
			long total = 0;
			
			for (int i = 1; i <= count; i++) 
			{
				total += i;
			}
			
			string totalString = $"({ DateTime.Now.ToLongTimeString() }) {total}";
			await context.Response.WriteAsync($"({DateTime.Now.ToLongTimeString()}) Total for {count}" + $" values:\n{totalString}\n");
		}
	}
}
```

Listing 17-4 creates a route that uses the endpoint, which is applied using the MapEndpoint extension methods created in Chapter 14.

Listing 17-4. Adding an Endpoint in the Program.cs File in the Platform Folder
```cs
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapEndpoint<Platform.SumEndpoint>("/sum/{count:int=1000000000}");
app.MapGet("/", async context => 
{
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```

Restart ASP.NET Core and use a browser to request https://localhost:5000/sum. The endpoint will sum 1,000,000,000 integer values and produce the result shown in Figure 17-2.
Reload the browser window, and the endpoint will repeat the calculation. Both the timestamps in the response change, as shown in the figure, indicating that every part of the response was produced fresh for each request.

> You may need to increase or decrease the default value for the route parameter based on the capabilities of your machine. Try to find a value that takes two or three seconds to produce the result — just long enough that you can tell when the calculation is being performed but not so long that you can step out for coffee while it happens.

Figure 17-2. An expensive response
![[zIMG-ASP.NET-Caching-Data-17-2.png]]

## Caching Data Values

ASP.NET Core provides a service that can be used to cache data values through the `IDistributedCache` interface. Listing 17-5 revises the endpoint to declare a dependency on the service and use it to cache calculated values.

Listing 17-5. Using the Cache Service in the SumEndpoint.cs File in the Platform Folder
```cs
using Microsoft.Extensions.Caching.Distributed;

namespace Platform 
{
	public class SumEndpoint 
	{
		public async Task Endpoint(HttpContext context, IDistributedCache cache) 
		{
			int count;
			int.TryParse((string?)context.Request.RouteValues["count"], out count);
			
			string cacheKey = $"sum_{count}";
			string totalString = await cache.GetStringAsync(cacheKey);
			
			if (totalString == null) 
			{
				long total = 0;
				for (int i = 1; i <= count; i++) 
				{
					total += i;
				}
				
				totalString = $"({ DateTime.Now.ToLongTimeString() }) {total}";
				await cache.SetStringAsync(cacheKey, totalString, new DistributedCacheEntryOptions 
				{
					AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(2)
				});
			}
			await context.Response.WriteAsync($"({DateTime.Now.ToLongTimeString()}) Total for {count}" + $" values:\n{totalString}\n");
		}
	}
}
```

The cache service can store only byte arrays, which can be restrictive but allows for a range of `IDistributedCache` implementations to be used. There are extension methods available that allow strings
to be used, which is a more convenient way of caching most data. Table 17-3 describes the most useful methods for using the cache.

Table 17-3. Useful IDistributedCache Methods

Name|Description
--|--
Get(String)|Gets a value with the given key.
GetAsync(String, CancellationToken)|Gets a value with the given key.
Set(String, Byte[], DistributedCacheEntryOptions)|Sets a value with the given key.
SetAsync(String, Byte[], DistributedCacheEntryOptions, CancellationToken)|Sets the value with the given key.
GetString(key)|This method returns the cached string associated with the specified key, or `null` if there is no such item.
GetStringAsync(key)|This method returns a `Task<string>` that produces the cached string associated with the key, or `null` if there is no such item.
SetString(key, value,options)|This method stores a string in the cache using the specified key. The cache entry can be configured with an optional `DistributedCacheEntryOptions` object.
SetStringAsync(key,value, options)|This method asynchronously stores a string in the cache using the specified key. The cache entry can be configured with an optional `DistributedCacheEntryOptions` object.
Refresh(key)|This method resets the expiry interval for the value associated with the key, preventing it from being flushed from the cache.
RefreshAsync(key)|This method asynchronously resets the expiry interval for the value associated with the key, preventing it from being flushed from the cache.
Remove(key)|This method removes the cached item associated with the key.
RemoveAsync(key)|This method asynchronously removes the cached item associated with the key.

By default, entries remain in the cache indefinitely, but the `SetString` and `SetStringAsync` methods accept an optional `DistributedCacheEntryOptions` argument that is used to set an expiry policy, which tells the cache when to eject the item. 
Table 17-4 shows the properties defined by the `DistributedCacheEntryOptions` class.

Table 17-4. The DistributedCacheEntryOptions Properties

Name|Description
--|--
AbsoluteExpiration|This property is used to specify an absolute expiry date.
AbsoluteExpirationRelativeToNow|This property is used to specify a relative expiry date.
SlidingExpiration|This property is used to specify a period of inactivity, after which the item will be ejected from the cache if it hasn’t been read.

The next step is to set up the cache service, as shown in Listing 17-6. 

Listing 17-6. Adding a Service in the Program.cs File in the Platform Folder
```cs
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddDistributedMemoryCache(opts => 
{
	opts.SizeLimit = 200;
});

var app = builder.Build();

app.MapEndpoint<Platform.SumEndpoint>("/sum/{count:int=1000000000}");
app.MapGet("/", async context => {
	await context.Response.WriteAsync("Hello World!");
});

app.Run();
```

`AddDistributedMemoryCache` is the same method I used in Chapter 16 to provide the data store for session data. This is one of the three methods used to select an implementation for the `IDistributedCache` service, as described in Table 17-5.

Table 17-5. The Cache Service Implementation Methods
Name|Description
--|--
AddDistributedMemoryCache|This method sets up an in-memory cache.
AddDistributedSqlServerCache|This method sets up a cache that stores data in SQL Server and is available when the `Microsoft.Extensions.Caching.SqlServer` package is installed. See the “Caching Responses” section for details.
AddStackExchangeRedisCache|This method sets up a Redis cache and is available when the `Microsoft.Extensions.Caching.Redis` package is installed.

Listing 17-6 uses the `AddDistributedMemoryCache` method to create an in-memory cache as the implementation for the `IDistributedCache` service. This cache is configured using the `MemoryCacheOptions` class, whose most useful properties are described in Table 17-6.

Table 17-6. Useful MemoryCacheOptions Properties
Name|Description
--|--
ExpirationScanFrequency|This property is used to set a `TimeSpan` that determines how often the cache scans for expired items.
SizeLimit|This property specifies the maximum number of items in the cache. When the size is reached, the cache will eject items.
CompactionPercentage|This property specifies the percentage by which the size of the cache is reduced when `SizeLimit` is reached.
