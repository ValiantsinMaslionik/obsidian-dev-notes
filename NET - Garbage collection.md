#NET/Platform 

---

## Links

[Garbage collection](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/)

## Releasing unmanaged resources

Each type can have a single **finalizer** that will be called by the >NET runtime when the resources need to be released.

```cs
public class Animal
{
	public Animal()
	{
		// allocate any unmanaged resources
	}
	
	// Finalizer aka Destructor
	~Animal()
	{
		// deallocate an unmanaged resources
	}
}
```

>![[zICO - Warning - 16.png]] The problem with only providing a finalizer is that the .NET garbage collector **requires two garbage collections** to completely release all allocated resources for this type.

## Releasing both managed and unmanaged resources

[MIcrosoft Docs](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/unmanaged)

```cs
public class Animal : IDisposable
{
	public Animal()
	{
		// allocate unmanaged resources
	}
	
	~Animal() // Finalizer
	{
		if(this.disposed) 
		{
			return;
		}
		
		Dispose(false);
	}

	bool disposed = false; // have resources been released?

	public void Dispose()
	{
		Dispose(true);

		// Notifies the GC that is no longer needs to run the finalizer, and removes the need for a second gerbage collection.
		GC.SuppressFinalize(this);
	}

	protected virtual void Dispose(bool disposing)
	{
		if(this.disposed)
		{
			return;
		}

		// deallocate the **unmanaged** resources
		// ...

		if(this.disposing)
		{
			// deallocate any other **managed** resources
			// ...
		}

		disposed = true;
	}
}
```

## Ensuring that Dispose is called

```cs
using(Animal a = new Animal())
{
	// ....
}

// Under the hood ---

Animal a = new Animal()
try
{
	// ...
}
finally
{
	a?.Dispose();
}
```