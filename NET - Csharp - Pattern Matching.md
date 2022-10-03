#NET/Csharp 

---

## Links

[Learn.MS](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/functional/pattern-matching)

One of the most useful recent additions to C# is support for pattern matching, which can be used to test that an object is of a specific type or has specific characteristics. This is another form of syntactic sugar, and it can dramatically simplify complex blocks of conditional statements.

## Type matching

The `is` keyword is used to perform a type test

#NET/Csharp/7
```cs
namespace LanguageFeatures.Controllers 
{
	public class HomeController : Controller 
	{
		public ViewResult Index() 
		{
			object[] data = new object[] { 275M, 29.95M, "apple", "orange", 100, 10 };
			decimal total = 0;
			for (int i = 0; i < data.Length; i++) 
			{
				if (data[i] is decimal d) 
				{
					total += d;
				}
			}
			return View("Index", new string[] { $"Total: {total:C2}" });
		}
	}
}
```

## Pattern Matching in switch Statements

Pattern matching can also be used in `switch` statements, which support the `when` keyword for restricting when a value is matched by a case statement.

#NET/Csharp/7
```cs
namespace LanguageFeatures.Controllers 
{
	public class HomeController : Controller 
	{
		public ViewResult Index() 
		{
			object[] data = new object[] { 275M, 29.95M, "apple", "orange", 100, 10 };
			decimal total = 0;
			for (int i = 0; i < data.Length; i++) 
			{
				switch (data[i]) 
				{
					case decimal decimalValue:
						total += decimalValue;
						break;
					case int intValue when intValue > 50:
						total += intValue;
						break;
				}
			}
			return View("Index", new string[] { $"Total: {total:C2}" });
		}
	}
}
```

#NET/Csharp/8
```cs
Stream s = File.Open(...);
switch(s)
{
	case FileStream writeableFile when s.CanWrite:
		writeableFile.smth();
		break;
	case FileStream readonlyFile:
		readonlyFile.smth();
		break;
	case MemoryStream ms:
		ms.smth();
		break;
	default:
		// always evaluated last despite its current position
		break;
	case null:
		// s is null
		break
}
```

#NET/Csharp/9
```cs
decimal flightCost = passenger switch
{
	FirstClassPassenger p => p.AirMiles switch
	{
		> 35000 => 1500M,
		> 15000 => 1750M,
		_		=> 2000M
	},
	BusinessCLassPassenger						  	=> 1000M,
	CoachClassPassenger p when p.CarryOnKG < 10.0	=> 500M,
	CoachClassPassenger								=> 650M,
	_												=> 800M
};
```

## Pattern matching in if statements

```cs
if(o is int i)
{
...
}
```

