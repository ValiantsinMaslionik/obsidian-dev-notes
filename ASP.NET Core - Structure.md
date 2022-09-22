#ASP_NET_CORE/Platform 

---

![[zIMG - ASP.NET - aspnet-structure.png]]

[[#MVC Framework]]
[[#Understanding Razor Pages]]
[[#Understanding Blazor]]
[[#Understanding the Utility Frameworks]]
[[#Understanding the ASP NET Core Platform]]
[[#SignalR]]
[[#gRPC]]

## MVC Framework

The MVC Framework was introduced in the early ASP.NET, long before .NET Core and .NET 6 were  
introduced. The original ASP.NET relied on a development model called Web Pages, which re-created the  
experience of writing desktop applications but resulted in unwieldy web projects that did not scale well.  
The MVC Framework was introduced alongside Web Pages with a development model that embraced the  
character of HTTP and HTML, rather than trying to hide it.

[#Term/MVC](app://obsidian.md/index.html#Term/MVC) stands for Model-View-Controller, which is a design pattern that describes the shape of an  
application. The MVC pattern emphasizes separation of concerns, where areas of functionality are defined  
independently, which was an effective antidote to the indistinct architectures that Web Pages led to.  
Early versions of the MVC Framework were built on the ASP.NET foundations that were originally  
designed for Web Pages, which led to some awkward features and workarounds. With the move to .NET  
Core, ASP.NET became ASP.NET Core, and the MVC Framework was rebuilt on an open, extensible, and  
cross-platform foundation.

The MVC Framework remains an important part of ASP.NET Core, but the way it is commonly used  
has changed with the rise of _single-page applications_ ([#Term/SPA](app://obsidian.md/index.html#Term/SPA)). In an SPA, 
the browser makes a single HTTP  request and receives an HTML document that delivers a rich client, typically written 
in a JavaScript client  such as Angular or React. The shift to SPAs means that the clean separation that the MVC Framework was  
originally intended for is not as important, and the emphasis placed on following the MVC pattern is no  
longer essential, even though the MVC Framework remains useful.

## Understanding Razor Pages

One drawback of the MVC Framework is that it can require a lot of preparatory work before an application
can start producing content. Despite its structural problems, one advantage of Web Pages was that simple
applications could be created in a couple of hours.

#Term/Razor_Pages takes the development ethos of Web Pages and implements it using the platform features
originally developed for the MVC Framework. Code and content are mixed to form self-contained pages;
this re-creates the speed of Web Pages development without some of the underlying technical problems
(although the issue of scaling up complex projects can still be an issue).

Razor Pages can be used alongside the MVC Framework, which is how I tend to use them. I write the
main parts of the application using the MVC Framework and use Razor Pages for the secondary features,
such as administration and reporting tools. You can see this approach in Chapters 7–11, where I develop a
realistic ASP.NET Core application called SportsStore.

## Understanding Blazor

The rise of JavaScript client-side frameworks can be a barrier for C# developers, who must learn a different—
and somewhat idiosyncratic—programming language. I have come to love JavaScript, which is as fluid and
expressive as C#. But it takes time and commitment to become proficient in a new programming language,
especially one that has fundamental differences from C#.

#Term/Blazor attempts to bridge this gap by allowing C# to be used to write client-side applications. There are
two versions of Blazor: Blazor Server and Blazor WebAssembly. Blazor Server is a stable and supported part
of ASP.NET Core, and it works by using a persistent HTTP connection to the ASP.NET Core server, where the
application’s C# code is executed. Blazor WebAssembly is an experimental release that goes one step further
and executes the application’s C# code in the browser. Neither version of Blazor is suited for all situations, as
I explain in Chapter 33, but they both give a sense of direction for the future of ASP.NET Core development.

## Understanding the Utility Frameworks

Two frameworks are closely associated with ASP.NET Core but are not used directly to generate HTML
content or data. Entity Framework Core is Microsoft’s object-relational mapping (ORM) framework, which
represents data stored in a relational database as .NET objects. Entity Framework Core can be used in any
.NET application, and it is commonly used to access databases in ASP.NET Core applications.
ASP.NET Core Identity is Microsoft’s authentication and authorization framework, and it is used to
validate user credentials in ASP.NET Core applications and restrict access to application features.

## Understanding the ASP.NET Core Platform

The ASP.NET Core platform contains the low-level features required to receive and process HTTP requests
and create responses. There is an integrated HTTP server, a system of middleware components to handle
requests, and core features that the application frameworks depend on, such as URL routing and the Razor
view engine.

Most of your development time will be spent with the application frameworks, but effective ASP.NET
Core use requires an understanding of the powerful capabilities that the platform provides, without which
the higher-level frameworks could not function. I demonstrate how the ASP.NET Core platform works in
detail in Part 2 of this book and explain how the features it provides underpin every aspect of ASP.NET Core
development.

## SignalR

#Term/SignalR is used to create low-latency communication channels between applications. It provides the 
foundation for the Blazor Server framework that I describe in Part 4 of this book, but SignalR is rarely used
directly, and there are better alternatives for those few projects that need low-latency messaging, such as 
Azure Event Grid ( #TODO/AddLink  ) or Azure Service Bus ( #TODO/AddLink  ).

## gRPC

#Term/gRPC is an emerging standard for cross-platform remote procedure calls (RPCs) over HTTP that was
originally created by Google (the g in gRPC) and offers efficiency and scalability benefits. gRPC may be the
future standard for web services, but it cannot be used in web applications because it requires low-level
control of the HTTP messages that it sends, which browsers do not allow. (There is a browser library that
allows gRPC to be used via a proxy server, but that undermines the benefits of using gRPC.) Until gRPC
can be used in the browser, its inclusion in ASP.NET Core is of interest only for projects that use it for
communication between back-end servers, for which many alternative protocols exist. I may cover gRPC in
future editions of this book but not until it can be used in the browser or becomes the dominant data-center
protocol.