#ASP_NET_CORE/MVC 

---

```cs
var builder = WebApplication.CreateBuilder(args);

// Add services
builder.Services.AddControllersWithViews();

var app = builder.Build();

// Map default controllers and routes
app.MapDefaultControllerRoute();

app.Run();
```