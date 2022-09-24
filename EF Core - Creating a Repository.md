#EF_Core 

---

The repository pattern is one of the most widely used, and it provides a consistent way to access the features presented by the
database context class. Not everyone finds a repository useful, but my experience is that it can reduce
duplication and ensures that operations on the database are performed consistently.
```
namespace SportsStore.Models 
{
	public interface IStoreRepository 
	{
		IQueryable<Product> Products { get; }
	}
}
```
This interface uses _IQueryable&gt;T&lt;_ to allow a caller to obtain a sequence of Product objects. The
_IQueryable&gt;T&lt;_ interface is derived from the more familiar _IQueryable&gt;T&lt;_ interface and represents a
collection of objects that can be queried, such as those managed by a database.
A class that depends on the _IStoreRepository_ interface can obtain Product objects without needing to
know the details of how they are stored or how the implementation class will deliver them.

To create an implementation of the repository interface, add a class file named EFStoreRepository.cs
in the Models folder and use it to define the class shown in Listing 7-19.
```
namespace SportsStore.Models 
{
	public class EFStoreRepository : IStoreRepository 
	{
		private StoreDbContext context;
		public EFStoreRepository(StoreDbContext ctx) 
		{
			context = ctx;
		}
		
		public IQueryable<Product> Products => context.Products;
	}
}
```
I’ll add additional functionality as I add features to the application, but for the moment, the repository
implementation just maps the Products property defined by the IStoreRepository interface onto the
Products property defined by the StoreDbContext class. The Products property in the context class returns
a _DbSet&gt;Product&lt;_ object, which implements the _IQueryable&gt;T&lt;_ interface and makes it easy to implement
the repository interface when using Entity Framework Core.
```
using Microsoft.EntityFrameworkCore;
using SportsStore.Models;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllersWithViews();
builder.Services.AddDbContext<StoreDbContext>(opts => 
{
	opts.UseSqlServer(builder.Configuration["ConnectionStrings:SportsStoreConnection"]);
});

builder.Services.AddScoped<IStoreRepository, EFStoreRepository>();

var app = builder.Build();
app.UseStaticFiles();
app.MapDefaultControllerRoute();

app.Run();
```
> The AddScoped method creates a service where each HTTP request gets its own repository object, which
> is the way that Entity Framework Core is typically used.