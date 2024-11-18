#aspnet_core/DI 

---

https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection#keyed-services

Starting with .NET 8, there is support for service registrations and lookups based on a key, meaning it's possible to register multiple services with a different key, and use this key for the lookup.

For example, consider the case where you have different implementations of the interface `IMessageWriter`: `MemoryMessageWriter` and `QueueMessageWriter`.

You can register these services using the overload of the service registration methods (seen earlier) that supports a key as a parameter:

```cs
services.AddKeyedSingleton<IMessageWriter, MemoryMessageWriter>("memory");
services.AddKeyedSingleton<IMessageWriter, QueueMessageWriter>("queue");
```

The `key` isn't limited to `string`, it can be any `object` you want, as long as the type correctly implements `Equals`.

In the constructor of the class that uses `IMessageWriter`, you add the [FromKeyedServicesAttribute](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.fromkeyedservicesattribute) to specify the key of the service to resolve:

```cs
public class ExampleService
{
    public ExampleService(
        [FromKeyedServices("queue")] IMessageWriter writer)
    {
        // Omitted for brevity...
    }
}
```