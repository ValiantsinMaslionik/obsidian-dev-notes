#aspnet_core/WebServices 

---

When you are responsible for developing both the web service and its client, the purpose of each action and its results are obvious and are usually written at the same time. If you are responsible for a web service that is consumed by third-party developers, then you may need to provide documentation that describes how the web service works. The **OpenAPI** specification, which is also known as **Swagger**, describes web services in a way that can be understood by other programmers and consumed programmatically. In this section, I demonstrate how to use *OpenAPI* to describe a web service and show you how to fine-tune that description.

## Resolving Action Conflicts

The *OpenAPI* discovery process requires a unique combination of the HTTP method and URL pattern for each action method. The process doesn’t support the `Consumes` attribute, so a change is required to the `ContentController` to remove the separate actions for receiving XML and JSON data, as shown in Listing 20-25.

Listing 20-25. Removing an Action in the ContentController.cs File in the Controllers Folder
```cs
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using WebApp.Models;

namespace WebApp.Controllers 
{
	[ApiController]
	[Route("/api/[controller]")]
	public class ContentController : ControllerBase 
	{
		private DataContext context;
		public ContentController(DataContext dataContext) 
		{
			context = dataContext;
		}
		
		[HttpGet("string")]
		public string GetString() => "This is a string response";
		
		[HttpGet("object/{format?}")]
		[FormatFilter]
		[Produces("application/json", "application/xml")]
		public async Task<ProductBindingTarget> GetObject() 
		{
			Product p = await context.Products.FirstAsync();
			return new ProductBindingTarget() 
			{
				Name = p.Name, 
				Price = p.Price, 
				CategoryId = p.CategoryId,
				SupplierId = p.SupplierId
			};
		}
		
		[HttpPost]
		[Consumes("application/json")]
		public string SaveProductJson(ProductBindingTarget product) 
		{
			return $"JSON: {product.Name}";
		}
		//[HttpPost]
		//[Consumes("application/xml")]
		//public string SaveProductXml([FromBody]ProductBindingTarget product) {
		// return $"XML: {product.Name}";
		//}
	}
}
```

Commenting out one of the action methods ensures that each remaining action has a unique combination of HTTP method and URL.

## Installing and Configuring the Swashbuckle Package

The *Swashbuckle* package is the most popular ASP.NET Core implementation of the OpenAPI specification and will automatically generate a description for the web services in an ASP.NET Core application. The package also includes tools that consume that description to allow the web service to be inspected and tested.
Open a new PowerShell command prompt, navigate to the folder that contains the *WebApp.csproj* file, and run the commands shown in Listing 20-26 to install the NuGet package.

Listing 20-26. Adding a Package to the Project
`dotnet add package Swashbuckle.AspNetCore --version 6.2.3`

Add the statements shown in Listing 20-27 to the Program.cs file to add the services and middleware provided by the *Swashbuckle* package.

Listing 20-27. Configuring Swashbuckle in the Program.cs File in the WebApp Folder
```cs
using Microsoft.EntityFrameworkCore;
using WebApp.Models;
using Microsoft.AspNetCore.Mvc;
using Microsoft.OpenApi.Models;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<DataContext>(opts => 
{
	opts.UseSqlServer(builder.Configuration["ConnectionStrings:ProductConnection"]);
	opts.EnableSensitiveDataLogging(true);
});

builder.Services.AddControllers()
builder.Services.AddNewtonsoftJson().
builder.Services.AddXmlDataContractSerializerFormatters();

builder.Services.Configure<MvcNewtonsoftJsonOptions>(opts => 
{
	opts.SerializerSettings.NullValueHandling = Newtonsoft.Json.NullValueHandling.Ignore;
});

builder.Services.Configure<MvcOptions>(opts => 
{
	opts.RespectBrowserAcceptHeader = true;
	opts.ReturnHttpNotAcceptable = true;
});

builder.Services.AddSwaggerGen(c => 
{
	c.SwaggerDoc("v1", 
		new OpenApiInfo 
		{ 
			Title = "WebApp", 
			Version = "v1"
		});
});

var app = builder.Build();

app.MapControllers();
app.MapGet("/", () => "Hello World!");

app.UseSwagger();
app.UseSwaggerUI(options => 
{
	options.SwaggerEndpoint("/swagger/v1/swagger.json", "WebApp");
});

var context = app.Services.CreateScope().ServiceProvider.GetRequiredService<DataContext>();
SeedData.SeedDatabase(context);

app.Run();
```

There are two features set up by the statements in Listing 20-27. The feature generates an OpenAPI description of the web services that the application contains. You can see the description by restarting ASP. NET Core and using the browser to request the URL *http://localhost:5000/swagger/v1/swagger.json*, which produces the response shown in Figure 20-7. The OpenAPI format is verbose, but you can see each URL that the web service controllers support, along with details of the data each expects to receive and the range of responses that it will generate.

Figure 20-7. The OpenAPI description of the web service
![[zIMG-asp.net-webservices-documantation-20-7.png]]

The second feature is a UI that consumes the OpenAPI description of the web service and presents the information in a more easily understood way, along with support for testing each action. Use the browser to request *http://localhost:5000/swagger*, and you will see the interface shown in Figure 20-8. You can expand each action to see details, including the data that is expected in the request and the different responses that the client can expect.

Figure 20-8. The OpenAPI explorer interface
![[zIMG-asp.net-webservices-documantation-20-8.png]]

## Fine-Tuning the API Description

Relying on the API discovery process can produce a result that doesn’t truly capture the web service. You can see this by examining the entry in the `Products` section that describes `GET` requests matched by the */api/Products/{id}* URL pattern. Expand this item and examine the response section, and you will see there is only one status code response that will be returned, as shown in Figure 20-9.

Figure 20-9. The data formats listed in the OpenAPI web service description
![[zIMG-asp.net-webservices-documantation-20-9.png]]

The API discovery process makes assumptions about the responses produced by an action method and doesn’t always reflect what can really happen. In this case, the `GetProduct` action method in the `ProductsController` class can return another response that the discovery process hasn’t detected.
```cs
[HttpGet("{id}")]
public async Task<IActionResult> GetProduct(long id) 
{
	Product? p = await context.Products.FindAsync(id);
	if (p == null) 
	{
		return NotFound();
	}
	return Ok(p);
}
```

If a third-party developer attempts to implement a client for the web service using the OpenAPI data, they won’t be expecting the `404 - Not Found` response that the action sends when it can’t find an object in the database.

### Running the API Analyzer

ASP.NET Core includes an analyzer that inspects web service controllers and highlights problems like the one described in the previous section. To enable the analyzer, add the elements shown in Listing 20-28 to the WebApp.csproj file. (If you are using Visual Studio, right-click the WebApp project item in the Solution Explorer and select Edit Project File from the pop-up menu.)

Listing 20-28. Enabling the Analyzer in the WebApp.csproj File in the WebApp Folder
```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
	<PropertyGroup>
		<TargetFramework>net6.0</TargetFramework>
		<Nullable>enable</Nullable>
		<ImplicitUsings>enable</ImplicitUsings>
	</PropertyGroup>
	<ItemGroup>
		<PackageReference Include="Microsoft.AspNetCore.Mvc.NewtonsoftJson" Version="6.0.0" />
		<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="6.0.0">
			<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
			<PrivateAssets>all</PrivateAssets>
		</PackageReference>
		<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="6.0.0" />
		<PackageReference Include="Swashbuckle.AspNetCore" Version="6.2.3" />
	</ItemGroup>
	<PropertyGroup>
		<IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
	</PropertyGroup>
</Project>
```

If you are using Visual Studio, you will see any problems detected by the API analyzer shown in the controller class file, as shown in Figure 20-10.

Figure 20-10. A problem detected by the API analyzer
![[zIMG-asp.net-webservices-documantation-20-10.png]]

If you are using Visual Studio Code, you will see warning messages when the project is compiled, either using the `dotnet build` command or when it is executed using the `dotnet run` command. When the project is compiled, you will see this message that describes the issue in the ProductController class:
```txt
Controllers\ProductsController.cs(28,9): warning API1000: Action method returns undeclared status code '404'. [C:\WebApp\WebApp.csproj]
1 Warning(s)
0 Error(s)
```

### Declaring the Action Method Result Type

To fix the problem detected by the analyzer, the `ProducesResponseType` attribute can be used to declare each of the response types that the action method can produce, as shown in Listing 20-29.

Listing 20-29. Declaring the Result in the ProductsController.cs File in the Controllers Folder
```cs
using Microsoft.AspNetCore.Mvc;
using WebApp.Models;

namespace WebApp.Controllers 
{
	[ApiController]
	[Route("api/[controller]")]
	public class ProductsController : ControllerBase 
	{
		private DataContext context;
		public ProductsController(DataContext ctx) 
		{
			context = ctx;
		}
		
		[HttpGet]
		public IAsyncEnumerable<Product> GetProducts() 
		{
			return context.Products.AsAsyncEnumerable();
		}
		
		[HttpGet("{id}")]
		[ProducesResponseType(StatusCodes.Status200OK)]
		[ProducesResponseType(StatusCodes.Status404NotFound)]
		public async Task<IActionResult> GetProduct(long id) 
		{
			Product? p = await context.Products.FindAsync(id);
			if (p == null) 
			{
				return NotFound();
			}
			return Ok(p);
		}
		// ...action methods omitted for brevity...
	}
}
```

Restart ASP.NET Core and use a browser to request *http://localhost:5000/swagger*, and you will see the description for the action method has been updated to reflect the `404` response, as shown in Figure 20-11.

Figure 20-11. Reflecting all the status codes produced by an action method
![[zIMG-asp.net-webservices-documantation-20-11.png]]
