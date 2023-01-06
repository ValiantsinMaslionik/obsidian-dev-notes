#EF_Core/asp_net_core

---

Endpoints and middleware components access Entity Framework Core data by declaring a dependency on the context class and using its `DbSet<T>` properties to perform LINQ queries. The LINQ queries are translated into SQL and sent to the database. The row data received from the database is used to create data model objects that are used to produce responses. Listing 17-27 updates the `SumEndpoint` class to use Entity Framework Core.

Listing 17-27. Using a Database in the SumEndpoint.cs File in the Platform Folder
```cs
using Microsoft.Extensions.Caching.Distributed;
using Platform.Services;
using Platform.Models;

namespace Platform 
{
	public class SumEndpoint 
	{
		public async Task Endpoint(HttpContext context, CalculationContext dataContext) 
		{
			int count;
			int.TryParse((string?)context.Request.RouteValues["count"], out count);
			long total = dataContext.Calculations?.FirstOrDefault(c => c.Count == count)?.Result ?? 0;
			
			if (total == 0) 
			{
				for (int i = 1; i <= count; i++) 
				{
					total += i;
				}
				dataContext.Calculations?.Add(
					new() 
					{
						Count = count, 
						Result = total
					});
				await dataContext.SaveChangesAsync();
			}
		
			string totalString = $"({ DateTime.Now.ToLongTimeString() }) {total}";
			await context.Response.WriteAsync($"({DateTime.Now.ToLongTimeString()}) Total for {count}" + $" values:\n{totalString}\n");
		}
	}
}
```

The endpoint uses the LINQ `FirstOrDefault` to search for a stored `Calculation` object for the calculation that has been requested like this:
```cs
dataContext.Calculations?.FirstOrDefault(c => c.Count == count)?.Result ?? 0;
```

If an object has been stored, it is used to prepare the response. If not, then the calculation is performed, and a new `Calculation` object is stored by these statements:
```cs
dataContext.Calculations?.Add(new Calculaton() { Count = count, Result = total});
await dataContext.SaveChangesAsync();
```

The `Add` method is used to tell Entity Framework Core that the object should be stored, but the update isn’t performed until the `SaveChangesAsync` method is called. To see the effect of the changes, restart ASP.NET Core MVC (without the INITDB argument if you are using the command line) and request the *http://localhost:5000/sum/10* URL. This is one of the calculations with which the database has been seeded, and you will be able to see the query sent to the database in the logging messages produced by the application.
```txt
Executing DbCommand [Parameters=[@__count_0='?' (DbType = Int32)],
CommandType='Text', CommandTimeout='30']
SELECT TOP(1) [c].[Id], [c].[Count], [c].[Result]
FROM [Calculations] AS [c]
WHERE [c].[Count] = @__count_0
```

If you request *http://localhost:5000/sum/100*, the database will be queried, but no result will be found. The endpoint performs the calculation and stores the result in the database before producing the result shown in Figure 17-5.

Once a result has been stored in the database, subsequent requests for the same URL will be satisfied using the stored data. You can see the SQL statement used to store the data in the logging output produced by Entity Framework Core.
```txt
Executing DbCommand [Parameters=[@p0='?' (DbType = Int32), @p1='?' (DbType = Int64)],
CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
INSERT INTO [Calculations] ([Count], [Result])
VALUES (@p0, @p1);
SELECT [Id]
FROM [Calculations]
WHERE @@ROWCOUNT = 1 AND [Id] = scope_identity();
```

> ![[zICO - Warning - 16.png]] Notice that the data retrieved from the database is not cached and that each request leads to a new SQL query. Depending on the frequency and complexity of the queries you require, you may want to cache data values or responses using the techniques described earlier in the chapter.