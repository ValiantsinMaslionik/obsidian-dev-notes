#NET/Csharp 

---

## Understanding records

Init-only properties provide some immutability to c#. You can take the concept further by using **records**. That makes the whole object immutable, so it acts as value,

Records should not have any state that changes after instantiation. Instead, the idea is that you create new records from existing ones with any changed state. This is called **non-destructive mutation**.

#NET/Csharp/9
```cs
public record ImmutableVehicle
{
	
	int Wheels; 	// public int Wheels { get; init; }
	string Color;	// public string Color { get; init; }
	string Brand;	// public string Brand { get; init; }
}

var car = new ImmutableVehicle
{
	Brand = "Mazda MX-5 RF",
	Color = "Soul Red Crystal Metallic",
	Wheels = 4
};

var repaintedCar = car with { Color = "Polymetal Grey Metallic"};

```

## Positional records

#NET/Csharp/9
```cs
public record ImmutableAnimal
{
	string Name;
	string Species;

	public ImmutableAnimal(string name, string species)
	{
		Name = name;
		Species = species;
	}

	public void Deconstruct(out string name, out string species)
	{
		name = Name;
		species = Species
	}
}

// The other possible ways to define a record
public data class ImmutableAnimalsDataClass(string Name, string Species);
public record ImmutableAnimalsRecord(string Name, string Species);

var oscar = new ImmutableAnimal("Oscar", "Labrador");
var (who, what) = oscar;
Console.WriteLine($"{who} is a {what}.");
```