#NET/Performance 

---

## Links

[Learn](https://learn.microsoft.com/en-us/dotnet/api/system.net.httplistener?view=netframework-4.8)

Provides a simple, programmatically controlled HTTP protocol listener.

### Multithreading

```csharp
var listener = new HttpListener(); 
listener.Rrefixes.Add("http://localhost:8080/"); 
listener.Start();

while (true) 
{ 
	var context = listener.GetContext(); 
	
	var thread = new Thread(() => ProcessRequest(context)); 
	thread.Start(); 
} 
```

### Pure asynchronous - best solution

```csharp
var listeqer = new HttpListener(); 
listener.Prefixes.Add("http://localhost:8080/"); 
listener.Start(); 

listener.BeginGetContext(AsyncProcessRequest, listener); 
Console.ReadKey(); 

private static void AsyncProcessRequest(IAsyncResult ar) 
{ 
	var listener = (HttpListener)ar.AsyncState; 
	listener.BeginGetContext(AsyncProcessRequest, listener); 

	// ...
	var context = listener.EndGetContext(ar); 
}
```
