#aspnet_core/Middleware

---

The purpose of the ASP.NET Core platform is to receive HTTP requests and send responses to them, which
ASP.NET Core delegates to middleware components. Middleware components are arranged in a chain,
known as the request pipeline.

When a new HTTP request arrives, the ASP.NET Core platform creates an object that describes it and
a corresponding object that describes the response that will be sent in return. These objects are passed to
the first middleware component in the chain, which inspects the request and modifies the response. The
request is then passed to the next middleware component in the chain, with each component inspecting the
request and adding to the response. Once the request has made its way through the pipeline, the ASP.NET
Core platform sends the response, as illustrated in Figure 12-2.

![[zIMG - ASP.NET - request pipeline.png]]
Figure 12-2. The ASP.NET Core request pipeline

Some components focus on generating responses for requests, but others are there to provide
supporting features, such as formatting specific data types or reading and writing cookies. ASP.NET Core
includes middleware components that solve common problems, as described in Chapters 15 and 16, and
I show how to create custom middleware components later in this chapter. If no response is generated
by the middleware components, then ASP.NET Core will return a response with the HTTP 404 Not Found
status code.