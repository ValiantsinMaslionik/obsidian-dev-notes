#NET/Logging

---

The [Debug](https://learn.microsoft.com/en-us/dotnet/api/system.diagnostics.debug?view=net-6.0) and [Trace](https://learn.microsoft.com/en-us/dotnet/api/system.diagnostics.trace?view=net-6.0) classes can write to any **trace listener**. Atrace listener is a type that can be configured to write output anywhere you like whenthe .Write() method is called.

 > You can make your own trace listener byinheriting from the [TraceLIstener](https://learn.microsoft.com/en-us/dotnet/api/system.diagnostics.tracelistener?view=net-6.0) type.
 
 ## Configuring trace listeners

```cs
...
// write to a text file in the project folder
TRace.LIsteners.Add(
	new TextWritterTraceListener(
		File.CretaeText("log.txt")))
// text writter is buffered, so this option calls Flush() on all listeners after writting
Trace.AutoFlush = true;
...
```

>![[zICO - Warning - 16.png]] When running with the **Dubug** configuration, both _Debug_ and _Trace_ are active and will show their output in DEBUG CONSOLE. When running with the **Release** confoguration, only the _Trace_ output is shown.

## Switching trace level

Number|Word|Description
--|--|--
0|Off|This will output nothing
1|Error|This will output only errors
2|Warning|This will output errors and warnings
3|Info|This will output errors, warnings, and information
4|Verbose|This output all levels

**appsettings.json**
```json
{
	"SomeSwitch": {
		"Level":"Info"
	}
}
```

**program.cs (.NET 5)**
```cs
...
var builder = new ConfigurationBuilder()
	.SetBasePath(Directory.GetCurrentDirectory())
	.AddJsonFIle("appsettings.json", optional: true, reloadOnChange: true);

var ts = new TraceSwitch(displayName: "SomeSwitch", description: "This switch is set via JSON config.");

IConfigurationRoot configuration = builder.Build();
configuration.GetSection("SomeSwitch").Bind(ts);

Trace.WriteLineIf(ts.TraceError, "Trace error");
Trace.WriteLineIf(ts.TraceVerbose, "Trace verbose");
```

