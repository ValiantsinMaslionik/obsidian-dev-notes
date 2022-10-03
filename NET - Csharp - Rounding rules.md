#NET/Csharp

---

> Banker's Rounding

Rule for rounding in c# is subtly different from the primary school rule:
- It always rouns *down* if the decimal part is less than the midpoint.5.
- It always rounds *up* if the decimal part is more than the midpoint.5.
- It will round *up* if the decimal part is the midpoint .5 and the non-decimal part is *odd*, but it will round *down* if the non-decimal part is *even*.

```cs
ToInt(9.49) is 9
ToInt(9.5) is 10
ToInt(9.51) is 10
ToInt(10.49) is 10
ToInt(10.5) is 10
ToInt(10.51) is 11
```

> **MidpointRounding.AlwaysFromZero** is the primary school roounding rule

```cs
Math.Round(9.5, 0, MidpointRounding.AlwaysFromZero); // 10
Math.Round(9.51, 0, MidpointRounding.AlwaysFromZero); // 10
Math.Round(10.5, 0, MidpointRounding.AlwaysFromZero); // 11
Math.Round(10.51, 0, MidpointRounding.AlwaysFromZero); // 11
```
