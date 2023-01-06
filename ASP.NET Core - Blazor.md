#aspnet_core/Blazor

---

The rise of JavaScript client-side frameworks can be a barrier for C# developers, who must learn a different 
and somewhat idiosyncratic programming language. I have come to love JavaScript, which is as fluid and
expressive as C#. But it takes time and commitment to become proficient in a new programming language,
especially one that has fundamental differences from C#.

#Term/Blazor attempts to bridge this gap by allowing C# to be used to write client-side applications. There are
two versions of Blazor: Blazor Server and Blazor WebAssembly. 

#Term/Blazor_Server is a stable and supported part of ASP.NET Core, and it works by using a persistent HTTP connection 
to the ASP.NET Core server, where the application’s C# code is executed. 

#Term/Blazor_WebAssembly is an experimental release that goes one step further and executes the application’s C# code 
in the browser. Neither version of Blazor is suited for all situations, as I explain in Chapter 33 #TODO/AddLink, but they 
both give a sense of direction for the future of ASP.NET Core development.
