#NET/Csharp 

---

## Common interfaces

Interface|Method(s)|Description
--|--|--
IComparable|CompareTo(other)|That defines a comparision methods that a type implements to order or sort its instances
IComparer|Comapre(first, second)|This defines a comparision method that a secondary type implements to order or sort instances of a primary type
IDisposable|Dispose()|This defines a disposal method to release unmanaged resources more efficiently than waiting for a finalizer
IFormattable|ToString(format, culture)|This define sa culure-aware method to format the value of an object into a string representation 
IFormatter|Serialize(stream,object)<br>Deserialize(stream)|This defines methods to convert an object to and from a stream of bytes for storage or transfer
IFormatProvider|GetFormat(type)|This defines a method to format inputs based on a language and region

## Interfaces with default implementations

#NET/Csharp/8

C# 8.0 allows an interface to add new members after release as long as they have a default implementation. C# purists dontlike the idea, but for practical reasons it is useful, and other langs such as Java and Swift enable similar techniques.

