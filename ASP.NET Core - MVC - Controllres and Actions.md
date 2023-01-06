#aspnet_core/MVC

---

The [[ASP.NET Core - Endpoints#^a65bd6|endpoint]] that may produce the response is an _#Term/action_, which is a method that is written in C#. 

An action is defined in a _#Term/controller_, which is a C# class that is derived from the _Microsoft.AspNetCore.Mvc.Controller_
class, the built-in controller base class. Each public method defined by a controller is an action, which means you can invoke 
the action method to handle an HTTP request.  ^8ffa5d

>The convention in ASP.NET Core projects is to put controller classes in a
>folder named Controllers, which was created by the template used to set up the project.

The project template added a controller to the Controllers folder to help jump-start development.  Controller classes contain 
a name followed by the word _Controller_, which means that when you see a file called HomeController.cs, you know that it
contains a controller called Home, which is the default controller that is used in ASP.NET Core applications.

```cs
using System.Diagnostics;
using Microsoft.AspNetCore.Mvc;
using FirstProject.Models;

namespace FirstProject.Controllers;

public class HomeController : Controller {
	private readonly ILogger<HomeController> _logger;
	public HomeController(ILogger<HomeController> logger) {
		_logger = logger;
	}

	public IActionResult Index() {
		return View();
	}
	
	public IActionResult Privacy() {
		return View();
	}
	
	[ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
	public IActionResult Error() {
		return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
	}
}
```
