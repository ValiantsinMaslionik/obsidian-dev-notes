#NET/Csharp 

---

```cs
var message = s switch
{
	FileStream wrireableFile when s.CanWrite => "Some message",
	FileStream readOnlyFile => "Some message",
	MemoryStream ms => "Some message",
	null => "Some message",
	_ => "Default case"
}
```