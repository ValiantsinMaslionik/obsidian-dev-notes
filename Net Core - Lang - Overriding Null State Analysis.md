#NET_CORE/Lang 

---

The C# compiler has a sophisticated understanding of when a variable can be null, but it doesn’t always get
it right, and there are times when you have a better understanding of whether a null value can arise than the
compiler. In these situations, the _null-forgiving operator_ (!) can be used to tell the compiler that a variable isn’t
null, regardless of what the null state analysis suggests

```
namespace LanguageFeatures.Controllers 
{
	public class HomeController : Controller 
	{
		public ViewResult Index() 
		{
			Product?[] products = Product.GetProducts();
			return View(new string[] 
			{ 
				products[0]!.Name 
			});
		}
	}
}
```