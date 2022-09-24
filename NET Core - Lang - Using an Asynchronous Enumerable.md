#NET_CORE/Lang 

---

An asynchronous enumerable describes a sequence of values that will be generated over time. To demonstrate the 
issue that this feature addresses, Listing 5-54 adds a method to the MyAsyncMethods class.
```
namespace LanguageFeatures.Models 
{
	public class MyAsyncMethods 
	{
		public async static Task<long?> GetPageLength() 
		{
			HttpClient client = new HttpClient();
			var httpMessage = await client.GetAsync("http://apress.com");
			return httpMessage.Content.Headers.ContentLength;
		}
		
		public static async Task<IEnumerable<long?>> GetPageLengths(List<string> output, params string[] urls) 
		{
			List<long?> results = new List<long?>();
			HttpClient client = new HttpClient();
			foreach (string url in urls) 
			{
				output.Add($"Started request for {url}");
				var httpMessage = await client.GetAsync($"http://{url}");
				results.Add(httpMessage.Content.Headers.ContentLength);
				output.Add($"Completed request for {url}");
			}
			return results;
		}
	}
}
```
The GetPageLengths method makes HTTP requests to a series of websites and gets their length. ==The
requests are performed asynchronously, but there is no way to feed the results back to the method’s caller as
they arrive. Instead, the method waits until all the requests are complete and then returns all the results in
one go==. 

In addition to the URLs that will be requested, this method accepts a List&gt;string&lt; to which I add
messages in order to highlight how the code works. Listing 5-55 updates the Index action method of the Home
controller to use the new method.
```
namespace LanguageFeatures.Controllers 
{
	public class HomeController : Controller 
	{
		public async Task<ViewResult> Index() 
		{
			List<string> output = new List<string>();
			foreach (long? len in await MyAsyncMethods.GetPageLengths(output, "apress.com", "microsoft.com", "amazon.com")) 
			{
				output.Add($"Page length: { len}");
			}
			return View(output);
		}
	}
}
```
The action method enumerates the sequence produced by the GetPageLengths method and adds
each result to the List&gt;string&lt; object, which produces an ordered sequence of messages showing the
interaction between the foreach loop in the Index method that processes the results and the foreach
loop in the GetPageLengths method that generates them may have different page lengths):
```
Started request for apress.com
Completed request for apress.com
Started request for microsoft.com
Completed request for microsoft.com
Started request for amazon.com
Completed request for amazon.com
Page length: 26973
Page length: 199526
Page length: 357777
```
You can see that the ==Index action method doesn’t receive the results until all the HTTP requests
have been completed==. This is the problem that the asynchronous enumerable feature solves, as shown in
the following listing.
```
namespace LanguageFeatures.Models 
{
	public class MyAsyncMethods 
	{
		public async static Task<long?> GetPageLength() 
		{
			HttpClient client = new HttpClient();
			var httpMessage = await client.GetAsync("http://apress.com");
			return httpMessage.Content.Headers.ContentLength;
		}
	
		public static async IAsyncEnumerable<long?> GetPageLengths(List<string> output, params string[] urls) 
		{
			HttpClient client = new HttpClient();
			foreach (string url in urls) 
			{
				output.Add($"Started request for {url}");
				var httpMessage = await client.GetAsync($"http://{url}");
				output.Add($"Completed request for {url}");
				yield return httpMessage.Content.Headers.ContentLength;
			}
		}
	}
}
```
The methods result is _IAsyncEnumerable&gt;long?&lt;_, which denotes an asynchronous sequence of nullable
long values. This result type has special support in .NET Core and works with standard _yield return_
statements, which isn’t otherwise possible because the result constraints for asynchronous methods conflict
with the _yield_ keyword. 
```
namespace LanguageFeatures.Controllers 
{
	public class HomeController : Controller 
	{
		public async Task<ViewResult> Index() 
		{
			List<string> output = new List<string>();
			await foreach (long? len in MyAsyncMethods.GetPageLengths(output, "apress.com", "microsoft.com", "amazon.com")) 
			{
				output.Add($"Page length: { len}");
			}
			return View(output);
		}
	}
}
```
The difference is that the _await_ keyword is applied before the foreach keyword and not before the call
to the async method. Restart ASP.NET Core and request http://localhost:5000; once the HTTP requests
are complete, you will see that the order of the response messages has changed, like this:
```
Started request for apress.com
Completed request for apress.com
Page length: 26973
Started request for microsoft.com
Completed request for microsoft.com
Page length: 199528
Started request for amazon.com
Completed request for amazon.com
Page length: 441398
```
The controller receives the next result in the sequence as it is produced. As I explain in Chapter 19 #TODO/AddLink , 
ASP.NET Core has special support for using IAsyncEnumerable&gt;T&lt; results in web services, allowing data values
to be serialized as the values in the sequence are generated.
