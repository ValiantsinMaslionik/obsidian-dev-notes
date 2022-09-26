#ASP_NET_CORE/MVC

---

## Overview

To produce an HTML response to a browser request, I need a _#Term/view_, which tells ASP.NET Core how to process the result
produced by the Index method into an HTML response that can be sent to the browser.

```cs
using Microsoft.AspNetCore.Mvc;

namespace FirstProject.Controllers {
	public class HomeController : Controller {
		public ViewResult Index() {
			return View("MyView");
		}
	}
}
```

> When I return a ViewResult object from an action method, I am instructing ASP.NET Core to _#Term/render_ a
view. I create the ViewResult by calling the View method, specifying the name of the view that I want to use,
which is MyView.

==Views are stored in the Views folder, organized into subfolders.==
Views that are associated with the Home controller, for example, are stored in a folder called Views/Home.
Views that are not specific to a single controller are stored in a folder called Views/Shared.

```html
@{
	Layout = null;
}

<!DOCTYPE html>
<html>
	<head>
		<meta name="viewport" content="width=device-width" />
		<title>Index</title>
	</head>
	<body>
		<div>
			Hello World (from the view)
		</div>
	</body>
</html>
```

The Razor expression _Layout_ #Term/Razor/Expression/Layout tells Razor that I chose not to use a layout, which is like a template
for the HTML that will be sent to the browser (and which I describe in Chapter 22 #TODO/AddLink ).

#Term/Razor/Expression/AT - (_@_) This is an expression that will be interpreted by _#Term/Razor_, which is the component that processes the
contents of views and generates HTML that is sent to the browser. Razor is a _#Term/view_engine_ (_view engine_), and the expressions
in views are known as _#Term/Razor/Expression_ (_Razor Expressions_).

## Dynamic Output

```cs
using Microsoft.AspNetCore.Mvc;

namespace FirstProject.Controllers {
	public class HomeController : Controller {
		public ViewResult Index() {
			int hour = DateTime.Now.Hour;
			string viewModel = hour < 12 ? "Good Morning" : "Good Afternoon";
			return View("MyView", viewModel);
		}
	}
}
```
```html
@model string

@{
	Layout = null;
}

<!DOCTYPE html>
<html>
	<head>
		<meta name="viewport" content="width=device-width" />
		<title>Index</title>
	</head>
	<body>
		<div>
			@Model World (from the view)
		</div>
	</body>
</html>
```

>The type of the view model is specified using the _@model_ #Term/Razor_Expressions/model expression, with a lowercase m. 
The view model value is included in the HTML output using the _@Model_ expression, with an uppercase M.

## Linking Action Methods

I want to be able to create a link from the Index view so that guests can see the RsvpForm view without having
to know the URL that targets a specific action method

```html
@{
	Layout = null;
}
<!DOCTYPE html>
<html>
	<head>
		<meta name="viewport" content="width=device-width" />
		<title>Party!</title>
	</head>
	<body>
		<div>
			<div>
				We're going to have an exciting party.<br />
				(To do: sell it better. Add pictures or something.)
			</div>
			<a asp-action="RsvpForm">RSVP Now</a>
		</div>
	</body>
</html>
```

The addition to the listing is an a element that has an _asp-action_ attribute. The attribute is an example
of a _#Term/Razor/TagHelper_ (tag helper) attribute, which is an instruction for Razor that will be performed when the view is rendered.

The _#Term/Razor/TagHelper/asp-action_ (_asp-action_) attribute is an instruction to add an href attribute to the a element that contains a URL for
an action method. I explain how tag helpers work in Chapters 25–27 #TODO/AddLink , but this tag helper tells Razor to insert a
URL for an action method defined by the same controller for which the current view is being rendered.

## Building the Form

```html
@model PartyInvites.Models.GuestResponse

@{
	Layout = null;
}
<!DOCTYPE html>
	<html>
		<head>
			<meta name="viewport" content="width=device-width" />
			<title>RsvpForm</title>
		</head>
	<body>
		<form asp-action="RsvpForm" method="post">
			<div>
				<label asp-for="Name">Your name:</label>
				<input asp-for="Name" />
			</div>
			<div>
				<label asp-for="Email">Your email:</label>
				<input asp-for="Email" />
			</div>
			<div>
				<label asp-for="Phone">Your phone:</label>
				<input asp-for="Phone" />
			</div>
			<div>
				<label asp-for="WillAttend">Will you attend?</label>
				<select asp-for="WillAttend">
				<option value="">Choose an option</option>
				<option value="true">Yes, I'll be there</option>
				<option value="false">No, I can't come</option>
				</select>
			</div>
			<button type="submit">Submit RSVP</button>
		</form>
	</body>
</html>
```

The @model expression specifies that the view expects to receive a GuestResponse object as its view
model. I have defined a label and input element for each property of the GuestResponse model class (or, in
the case of the WillAttend property, a select element). 

Each element is associated with the model property using the #Term/Razor/TagHelper/asp-for  (_asp-for_) attribute, which is another tag helper attribute. The tag helper attributes configure the elements to tie them to the view model object. 
Here is an example of the HTML that the tag helpers produce:
```html
<p>
	<label for="Name">Your name:</label>
	<input type="text" id="Name" name="Name" value="">
</p>
```

The asp-for attribute on the label element sets the value of the for attribute. The asp-for attribute on
the input element sets the id and name elements. This may not look especially useful, but you will see that
associating elements with a model property offers additional advantages as the application functionality is defined.

Of more immediate use is the asp-action attribute applied to the form element, which uses the application’s
[[ASP.NET Core - Routing|URL routing]] configuration to set the action attribute to a URL that will target a specific [[ASP.NET Core - MVC - Controllres and Actions#^8ffa5d|action]] method, like this:
```html
<form method="post" action="/Home/RsvpForm">
```

==As with the helper attribute I applied to the a element, the benefit== of this approach is that when you can
change the system of URLs that the application uses, the content generated by the tag helpers will reflect the
changes automatically.