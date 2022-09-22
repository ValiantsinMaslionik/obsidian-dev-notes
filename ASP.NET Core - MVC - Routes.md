#ASP_NET_CORE/MVC 

---

The ASP.NET Core routing system is responsible for selecting the endpoint that will handle an HTTP request.

A _#Term/route_ is a rule that is used to decide how a request is handled. 

When the project was created, a default rule was created to get started.  You can request any of the following 
URLs, and they will be dispatched to the Index action defined by the Home controller:

```
/
/Home
/Home/Index
```

So, when a browser requests http://yoursite/ or http://yoursite/Home, it gets back the output from HomeController’s Index method.  
If you append /Home or /Home/Index to the URL and press Return, you will see the same Hello World result from the application.
