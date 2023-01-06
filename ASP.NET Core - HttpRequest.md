#aspnet_core 

---

The ASP.NET Core platform is responsible for processing the HTTP request to create the HttpRequest
object, which means that middleware and endpoints don’t have to worry about the raw request data.

Name|Description
--|--
Body|This property returns a stream that can be used to read the request body.
ContentLength|This property returns the value of the Content-Length header.
ContentType|This property returns the value of the Content-Type header.
Cookies|This property returns the request cookies.
Form|This property returns a representation of the request body as a form.
Headers|This property returns the request headers.
IsHttps|This property returns true if the request was made using HTTPS.
Method|This property returns the HTTP verb—also known as the HTTP method—used for the request.
Path|This property returns the path section of the request URL.
Query|This property returns the query string section of the request URL as key-value pairs.
