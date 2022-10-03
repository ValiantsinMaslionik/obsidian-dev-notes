#NET/Csharp 

---

The ASP.NET Core project templates enable a feature named implicit usings, which define global using
statements for these commonly required namespaces:

```txt
System
System.Collections.Generic
System.IO
System.Linq
System.Net.Http
System.Net.Http.Json
System.Threading
System.Threading.Tasks
Microsoft.AspNetCore.Builder
Microsoft.AspNetCore.Hosting
Microsoft.AspNetCore.Http
Microsoft.AspNetCore.Routing
Microsoft.Extensions.Configuration
Microsoft.Extensions.DependencyInjection
Microsoft.Extensions.Hosting
Microsoft.Extensions.Logging
```

_using_ statements are not required for these namespaces, which are available throughout the
application. These namespaces don’t cover all of the ASP.NET Core features, but they do cover the basics,
which is why no explicit using statements are required in the Program.cs file, for example.
