#NET/Csharp

---

```cs
try
{
	checked
	{
		int x = int.MaxValue - 1;
		x++;
		x++;
	}
}
catch (OwerflowException)
{
...
}
```
```cs
unchecked
{
	int x = int.MaxValue - 1;
	x++;
	x++;
}

```