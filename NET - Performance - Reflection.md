#NET/Performance

---

>![[zICO - Exclamation - 16.png]] All .Compile() methods must be cached in order to aviod performance degradation!

## Optimize method calls

```csharp
piublic class Command
{
	private int Execute() => 42;
}
```

### Using reflection - slowest

```csharp
public static int CallExecute(Command command)
{
	int typeof(Command)
		.GetMethod("Execute", BindingFlags.NonPublic | BindingFlags.Instance)
		.Invoke(command, null);
}

```

### Using reflection cache - a little bit faster
```csharp
public static class ReflectionCached 
{ 
	private static Methodlnfo ExecuteMethod { get; } = 
		typeof(Command).GetMethod("Execute", BindingFlags.NonPublic | BindingFlags.Instance); 
	
	public static int CallExecute(Command command) = (int)ExecuteMethod.Invoke(command, null); 
} 
```

### Using Delegate.CreateDelegate - even faster

[Learn](https://learn.microsoft.com/en-us/dotnet/api/system.delegate.createdelegate?view=net-6.0#system-delegate-createdelegate(system-type-system-reflection-methodinfo))

>![[zICO - Warning - 16.png]] This solution Is not suitable fro contructors, for example

```csharp
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

### Expression trees - the fastest

[Learn](https://learn.microsoft.com/en-us/dotnet/api/system.linq.expressions.expression.call?view=net-6.0#system-linq-expressions-expression-call(system-linq-expressions-expression-system-reflection-methodinfo))

```csharp
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

OR

```csharp
public static Delegate CreateMethod(MethodInfo method) 
{ 
	var parameters = method.GetParameters()
		.Select(p => Expression.Parameter(p.ParameterType, p.Name))
		.ToArray(); 
	var call = Expression.Call(null, method, parameters);
	
	return Expression.Lambda(call, parameters)
		.Compile();  
} 
```

![[zIMG-NET-performance-reflection-metthod-call.png]]

## Optimize object creation

Expression trees is much faster than Activator.CreateInstance

```csharp
var newExp = Expression.New(ctor,argsExp); 
var lambda = Expression.Lambda(typeof(ObjectActivator<T>), newExp, param);
var compiled = (ObjectActivator<T>)lambda.Compile(); 
```

![[zIMG-NET-performance-expressiontree-new.png]]

## Optimize calls to getters and setters

### Getter

```csharp
public static Func<TObject, TProperty> PropertyGetter<TObject, TProperty>(string propertyName) 
{ 
	var paramExpression = Expression.Parameter(typeof(TObject), "value"); 
	var propertyGetterExpression = Expression.Property( paramExpression, propertyName); 
	var result = Expression.Lambda<Func<TObject, TProperty>>( propertyGetterExpression, paramExpression).Compile(); 
	return result; 
}
```

### Setter

```csharp
var propertyExpression = Expression.Property(paramExpression, propertyName); 
var result = Expression.Lambda<Action<TObject, TProperty>>(
	(Expression.Assign(propertyExpression, paramExpression2), paramExpression, paramExpression2)
	.Compile(); 
```

