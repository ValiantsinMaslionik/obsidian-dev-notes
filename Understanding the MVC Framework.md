The MVC Framework was introduced in the early ASP.NET, long before .NET Core and .NET 6 were  
introduced. The original ASP.NET relied on a development model called Web Pages, which re-created the  
experience of writing desktop applications but resulted in unwieldy web projects that did not scale well.  
The MVC Framework was introduced alongside Web Pages with a development model that embraced the  
character of HTTP and HTML, rather than trying to hide it.  

#Term/MVC stands for Model-View-Controller, which is a design pattern that describes the shape of an  
application. The MVC pattern emphasizes separation of concerns, where areas of functionality are defined  
independently, which was an effective antidote to the indistinct architectures that Web Pages led to.  
Early versions of the MVC Framework were built on the ASP.NET foundations that were originally  
designed for Web Pages, which led to some awkward features and workarounds. With the move to .NET  
Core, ASP.NET became ASP.NET Core, and the MVC Framework was rebuilt on an open, extensible, and  
cross-platform foundation.  

The MVC Framework remains an important part of ASP.NET Core, but the way it is commonly used  
has changed with the rise of single-page applications (SPAs). In an #Term/SPA, the browser makes a single HTTP  
request and receives an HTML document that delivers a rich client, typically written in a JavaScript client  
such as Angular or React. The shift to SPAs means that the clean separation that the MVC Framework was  
originally intended for is not as important, and the emphasis placed on following the MVC pattern is no  
longer essential, even though the MVC Framework remains useful.

---

[[ASP.NET MVC]]