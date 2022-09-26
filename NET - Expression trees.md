#NET/Platform/ExpressionTrees

---

## Links

[Microdoft Learn](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/expression-trees/)
[Working with Expression Trees In C#](zDOC_NET-Working-with-Expression-Trees.mhtml)
[[NET - Performance - Reflection|Optimizing Reflection performance]]

## Implementing generic operators

```csharp
public static class ThreeFourths 
{ 
	private static class Impl<T> 
	{
		public static Func<T, T> Of { get; }
		
		static Impl() 
		{ 
			var param = Expression.Parameter(typeof(T)); 
			
			var three = Expression.Convert(Expression.Constant(3), typeof(T)); 
			var four = Expression.Convert(Expression.Constant(4), typeof(T));
			
			var operation = Expression.Divide(Expression.Multiply(param, three), four);
			var lambda = Expression.Lambda<Func<T, J>>(operation, param); 
			
			Of = lambda.Compile(); 
		}
	}	
	
	public static T Of<T>(T x) => Impl<T>.Of(x);
}
```

> Works much faster than dynamic #TODO/AddLink 

![[zIMG-NET-performance-expressiontree-generic-operator.png]]

## Executing DSLs

Implementing simple calculator that can pasre expression from a string (using [[NET - Libraries|sprache]] library).
Also, there is a faster version of the .Compile() method provided by the [[NET - Libraries|FastExpressionCompiler]] library.

```csharp
public static class SimpleCalculator 
{ 
	private static readonly Parser<Expression> Constant = 
		Parse.Decimallnvariant
		.Select(n => double.Parse(n, CultureInfo.InvariantCulture))
		.Select(n => Expression.Constant(n, typeof(double)))
		.Token(); 
		
	private static readonly Parser<ExpressionType> Operator = 
		Parse.Char('+').Return(ExpressionType.Add)
		.Or(Parse.Char('-').Return(ExpressionType.Subtract))
		.Or(Parse.Char('*').Return(ExpressionType.Multiply))
		.Or(Parse.Char('/').Return(ExpressionType.Divide)); 
	
	private static readonly Parser<Expression> Operation = 
		Parse.ChainOperator(Operator, Constant, Expression.MakeBinary); 
	
	private static readonly Parser<Expression> FullExpression = 
		Operation.Or(Constant)
		.End(); 
	
	public static double Run(string expression) 
	{ 
		var operation = FullExpression.Parse(expression);
		var func = Expression.Lambda<Func<double>>(operation).Compile(); 
		return func(); 
	}
}

var a = SImpleCalculator.Run("2 + 2"); // 4
var b = SImpleCalculator.Run("3.15 * 5 + 2"); // 17.75
var c = SImpleCalculator.Run("1 / 2 * 3"); // 1.5
```

## Obtaining ET from lambdas

[Learm](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/expression-trees/#creating-expression-trees-from-lambda-expressions)

```csharp
Expression<Func<int, int, int>> divExpr = (a, b) => a / b;`
var divFunc = divExpr.Compile();
var c = divFunc(10, 5); // 2
```

### Limitations

- Only direct lambda is allowed
```csharp
Func<int, int, int> divFunc = (a, b) => a / b;
Expression<Func<int, int, int>> divExpr = divFunc; // Error
```
- Code block is not allowed
```csharp
Expression<Func<int, int, int>> divExpr = (a, b) => // Error
{
	var x = a / b;
	var y = x + a;
	return y;
};
```
- Other limitations
![[zIMG-NET-expressiontree-from-lambda-limitations.png]]

## Identifying type members

![[zIMG-NET-expressiontree-identify-type-members-01.png]]

### Possible solution using reflection

![[zIMG-NET-expressiontree-identify-type-members-02.png]]

### Solution using ET

![[zIMG-NET-expressiontree-identify-type-members-03.png]]

## Preserving source code in assertions

Let's imagine that we want to see source code that failed assertion insted of a message

![[zIMG-NET-expressiontree-get-source-code-01.png]]

### Solution

> .ToReadableString() - extension method provided by the [[NET - Libraries|ReadableExpressions]] library

![[zIMG-NET-expressiontree-get-source-code-02.png]]

### Outcome

![[zIMG-NET-expressiontree-get-source-code-03.png]]

## Traversion and rewriting ETs

The feature is implemented by the [ExpressionVisitor](https://learn.microsoft.com/en-us/dotnet/api/system.linq.expressions.expressionvisitor?view=net-6.0) class.

### Visitor implementation to log visited expressions
![[zIMG-NET-expressiontree-expressionvisitor-01.png]]
![[zIMG-NET-expressiontree-expressionvisitor-02.png]]

### Visitor implementation to rewrite expressions

![[zIMG-NET-expressiontree-expressionvisitor-03.png]]
![[zIMG-NET-expressiontree-expressionvisitor-04.png]]

## Transplitting code into a different language

For example, we want transplit c# code into the f# equialent.
Something like this

![[zIMG-NET-expressiontree-expressionvisitor-transplitting-01.png]]

### Possible implementation

![[zIMG-NET-expressiontree-expressionvisitor-transplitting-02.png]]
![[zIMG-NET-expressiontree-expressionvisitor-transplitting-03.png]]

### Outcome

![[zIMG-NET-expressiontree-expressionvisitor-transplitting-04.png]]