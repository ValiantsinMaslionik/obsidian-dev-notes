#NET/Lang 

---

Care must be taken to apply the question mark correctly, especially when dealing with arrays and
collections. 

A variable of type Product?] denotes an array that can contain Product or null values
but that won’t be null itself:
```
Product?[] arr1 = new Product?[] { kayak, lifejacket, null }; // OK
Product?[] arr2 = null; // Not OK
```

A variable of type Product[]? is an array that can hold only Product values and not null values, but
the array itself may be null:
```
Product[]? arr1 = new Product?[] { kayak, lifejacket, null }; // Not OK
Product[]? arr2 = null; // OK
```

A variable of type Product?[]? is an array that can contain Product or null values and that can
itself be null:
```
Product?[]? arr1 = new Product?[] { kayak, lifejacket, null }; // OK
Product?[]? arr2 = null; // Also OK
```
