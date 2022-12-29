#EF_Core 

---

For this chapter, I am going to define the data model using C# classes and use Entity Framework Core to create the database and schema. Create the Platform/Models folder and add to it a class file called Calculation.cs with the contents shown in Listing 17-18.

Listing 17-18. The Contents of the Calculation.cs File in the Platform/Models Folder
```cs
namespace Platform.Models 
{
	public class Calculation 
	{
		public long Id { get; set; }
		public int Count { get; set; }
		public long Result { get; set; }
	}
}
```

You can see more complex data models in other chapters, but for this example, I am going to keep with the theme of this chapter and model the calculation performed in earlier examples. The Id property will be used to create a unique key for each object stored in the database, and the `Count` and `Result` properties will describe a calculation and its result.

>Entity Framework Core uses a context class that provides access to the database. 

Add a file called CalculationContext.cs to the Platform/Models folder with the content shown in Listing 17-19.

Listing 17-19. The Contents of the CalculationContext.cs File in the Platform/Models Folder
```cs
using Microsoft.EntityFrameworkCore;

namespace Platform.Models 
{
	public class CalculationContext : DbContext 
	{
		public CalculationContext(DbContextOptions<CalculationContext> opts)
			: base(opts) 
		{ }
		
		public DbSet<Calculation> Calculations => Set<Calculation>();
	}
}
```

The `CalculationContext` class defines a constructor that is used to receive an options object that is passed on to the base constructor. 
The `Calculations` property provides access to the `Calculation` objects that Entity Framework Core will retrieve from the database.