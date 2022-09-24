#NET/Performance/Reflection

---

[[#Optimize method calls]]

## Optimize method calls

```
piublic class Command
{
	private int Execute() => 42;
}
```

[[#^e069d3|Reflection cache]]
[[#^ca3b4c|Delegate.CreateDelegate()]]
[[#^39d070|Expression trees]]

- Usng reflection (slowest)
```
public static int CallExecute(Command command)
{
	int typeof(Command)
		.GetMethod("Execute", BindingFlags.NonPublic | BindingFlags.Instance)
		.Invoke(command, null);
}

```

- Using reflection cache (a little bit faster) ^e069d3
```
public static class ReflectionCached 
{ 
	private static Methodlnfo ExecuteMethod { get; } = 
		typeof(Command).GetMethod("Execute", BindingFlags.NonPublic | BindingFlags.Instance); 
	
	public static int CallExecute(Command command) = (int)ExecuteMethod.Invoke(command, null); 
} 
```

- Using [Delegate.CreateDelegate](https://learn.microsoft.com/en-us/dotnet/api/system.delegate.createdelegate?view=net-6.0#system-delegate-createdelegate(system-type-system-reflection-methodinfo)) (even faster) ^ca3b4c

>**This solution Is not suitable fro contructors, for example**

```
public static class ReflectionDelegate 
{ 
	private static Methodlnfo ExecuteMethod { get; } = 
		typeof(Command).GetMethod("Execute", BindingFlags.NonPublic | BindingFlags.Instance);

	// ? Delegate that describies a call of the ExecuteMethod.Invoke()
	private static Func<Command, int> Impl { get; } = 
		(Func<Command, int>)Delegate.CreateDelegate(typeof(Func<Command, int>), ExecuteMethod); 
	
	public static int CallExecute(Command command) => Impl(command); 
}
```
- Expression trees (the fastest) ^39d070

[Expression.Call](https://learn.microsoft.com/en-us/dotnet/api/system.linq.expressions.expression.call?view=net-6.0#system-linq-expressions-expression-call(system-linq-expressions-expression-system-reflection-methodinfo))

```
public static class ExpressionTrees 
{ 
	private static Methodlnfo ExecuteMethod { get; } = 
		typeof(Command).GetMethod("Execute", BindingFlags.NonPublic 1 BindingFlags.Instance); 
		
	private static Func<Command, int> Impl { get; }

	// Lazy thread-safe initialization via static constructor
	static ExpressionTrees()
	{ 
		var instance = Expression.Parameter(typeof(Command)); 
		var call = Expression.Call(instance, ExecuteMethod); 
		Impl = Expression
				.Lambda<Func<Command, int>>(call, instance)
				.Compile();
	} 
	
	public static int CallExecute(Command command) => Impl(command); 
}
```

![[zIMG-NET-performance-reflection-metthod-call.png]]