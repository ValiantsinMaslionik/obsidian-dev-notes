#EF_Core 

---

Entity Framework Core doesn’t include parameter values in the logging messages it produces, which is why the logging output contains question marks, like this:
```txt
Executing DbCommand [Parameters=[@__count_0='?' (DbType = Int32)], CommandType='Text', CommandTimeout='30']
```

The data is omitted as a precaution to prevent sensitive data from being stored in logs. If you are having problems with queries and need to see the values sent to the database, then you can use the `EnableSensitiveDataLogging` method when configuring the database context, as shown in Listing 17-28.

Listing 17-28. Enabling Sensitive Data Logging in the Program.cs File in the Platform Folder
```cs
builder.Services.AddDbContext<CalculationContext>(opts => 
{
	opts.UseSqlServer(builder.Configuration["ConnectionStrings:CalcConnection"]);
	opts.EnableSensitiveDataLogging(true);
});
```

Restart ASP.NET Core MVC and request the *http://localhost:5000/sum/100* URL again. When the request is handled, Entity Framework Core will include parameter values in the logging message it creates to show the SQL query, like this:
```txt
Executed DbCommand (40ms) [Parameters=[@__count_0='100'], CommandType='Text',
CommandTimeout='30']
SELECT TOP(1) [c].[Id], [c].[Count], [c].[Result]
FROM [Calculations] AS [c]
WHERE [c].[Count] = @__count_0
```

>![[zICO - Exclamation - 16.png]] This is a feature that should be used with caution because logs are often accessible by people who would not usually have access to the sensitive data that applications handle, such as credit card numbers and account details.