#ASP_NET_CORE/Platform 

---

The HttpResponse object describes the HTTP response that will be sent back to the client when
the request has made its way through the pipeline. Table 12-6 describes the most useful members of the
HttpResponse class. The ASP.NET Core platform makes dealing with responses as easy as possible, sets
headers automatically, and makes it easy to send content to the client.

Name|Description
--|--
ContentLength|This property sets the value of the Content-Length header.
ContentType|This property sets the value of the Content-Type header.
Cookies|This property allows cookies to be associated with the request.
**HasStarted**|This property returns true if ASP.NET Core has started to send the response headers to the client, <br>after which it is not possible to make changes to the status code or headers.
Headers|This property allows the response headers to be set.
StatusCode|This property sets the status code for the response.
WriteAsync(data)|This asynchronous method writes a data string to the response body.
Redirect(url)|This method sends a redirection response.

^c49c34

