#NET/Csharp

---

Sometimes you want to treat properties like `readonly` fields so they can be set during instantiation but not after.

#NET/Csharp/9
```cs
public class ImmutablePerson
{
	public string FirstName { get; init; }
	public string LastName { get; init; }
}

var jeff = new ImmutablePerson
{
	FirstName = "Jeff",
	LastName = "Winger"
}

// Compiler error here!
jeff.FirstName = "Geoff";
```
