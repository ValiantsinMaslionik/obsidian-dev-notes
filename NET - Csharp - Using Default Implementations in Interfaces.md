#NET/Csharp 


Useful feature in order to extend an existing interface without updating the code that use it code

```cs
namespace LanguageFeatures.Models 
{
	public interface IProductSelection 
	{
		IEnumerable<Product>? Products { get; }
		
		// New method with default implementation
		IEnumerable<string>? Names => Products?.Select(p => p.Name);
	}
}
```