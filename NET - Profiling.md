#NET/Performance 

---

## Understand profiler performance collection methods
[Understand profiler performance collection methods](https://learn.microsoft.com/en-us/visualstudio/profiling/understanding-performance-collection-methods-perf-profiler?view=vs-2022)

## Instrumentation
[Overview](https://learn.microsoft.com/en-us/visualstudio/profiling/instrumentation-overview?view=vs-2022)
[Instrument your .NET applications in Visual Studio](https://learn.microsoft.com/en-us/visualstudio/profiling/instrumentation?view=vs-202)]
[Instrument a stand-alone .NET Framework component and collect timing data with the profiler from the command line](https://learn.microsoft.com/en-us/visualstudio/profiling/instrument-dotnet-framework-component-and-collect-timing-data?view=vs-2022)

## How to find out what profiler is already attached to a .NET process

Еo find out what profiler is already attached, you can open *Process Explorer* tool and look for your process in the list of running processes. 
Right-click on the process, and then click on the *Environment* tab, then check for a "*COR_ENABLE_PROFILING*" and "*COR_PROFILER*" as in the screenshot below
![[zImg-NET-Profiler-HowToFindOutAttachedProfiler.png]]

## Work flow of diagnosing memory performance issues

https://devblogs.microsoft.com/dotnet/work-flow-of-diagnosing-memory-performance-issues-part-0/
[Local Document - Part 0](zDOC_NET-Profiling-Wworkflow-Diagnostic-Memory-Performance-P0.mhtml)

https://devblogs.microsoft.com/dotnet/work-flow-of-diagnosing-memory-performance-issues-part-1/
[Local Document - Part 1](zDOC_NET-Profiling-Wworkflow-Diagnostic-Memory-Performance-P1.mhtml)

https://devblogs.microsoft.com/dotnet/work-flow-of-diagnosing-memory-performance-issues-part-2/
[Local Document - Part 2](zDOC_NET-Profiling-Wworkflow-Diagnostic-Memory-Performance-P2.mhtml)