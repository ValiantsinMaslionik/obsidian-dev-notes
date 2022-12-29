
#ASP_NET_CORE/RESTfull

---

## Understanding RESTful Web Services

Web services respond to HTTP requests with data that can be consumed by clients, such as JavaScript applications. There are no hard-and-fast rules for how web services should work, but the most common approach is to adopt the **Representational State Transfer (REST)** pattern. There is no authoritative specification for REST, and there is no consensus about what constitutes a RESTful web service, but there are some common themes that are widely used for web services. The lack of a detailed specification leads to endless disagreement about what REST means and how RESTful web services should be created, all of which can be safely ignored if the web services you create work for your projects.

## Understanding Request URLs and Methods

The core premise of REST—and the only aspect for which there is broad agreement—is that a web service defines an API through a combination of URLs and HTTP methods such as `GET` and `POST`, which are also known as the **HTTP verbs**. The method specifies the type of operation, while the URL specifies the data object or objects that the operation applies to.

As an example, here is a URL that might identify a `Product` object in the example application:
``/api/products/1`

This URL may identify the `Product` object that has a value of 1 for its `ProductId` property. The URL identifies the `Product`, but it is the HTTP method that specifies what should be done with it. Table 19-3 lists the HTTP methods that are commonly used in web services and the operations they conventionally represent.

Table 19-3. HTTP Methods and Operations
HTTP|Method Description
--|--
GET|This method is used to retrieve one or more data objects.
POST|This method is used to create a new object.
PUT|This method is used to update an existing object.
PATCH|This method is used to update part of an existing object.
DELETE|This method is used to delete an object.

## Understanding JSON

Most RESTful web services format the response data using the **JavaScript Object Notation (JSON)** format. JSON has become popular because it is simple and easily consumed by JavaScript clients. JSON is described
in detail at www.json.org, but you don’t need to understand every aspect of JSON to create web services because ASP.NET Core provides all the features required to create JSON responses.

## UNDERSTANDING THE ALTERNATIVES TO RESTFUL WEB SERVICES

REST isn’t the only way to design web services, and there are some popular alternatives. 

**GraphQL** is most closely associated with the *React* JavaScript framework, but it can be used more widely. Unlike REST web services, which provide specific queries through individual combinations of a URL and an HTTP method, *GraphQL* provides access to all an application’s data and lets clients query for just the data they require in the format they require. GraphQL can be complex to set up—and can require more sophisticated clients—but the result is a more flexible web service that puts the developers of the client  in control of the data they consume. GraphQL isn’t supported directly by ASP.NET Core, but there are .NET implementations available. See [https://graphql.org/code/#c-net](https://graphql.org/code/#c-net) for more detail.

A new alternative is **gRPC**, a full remote procedure call framework that focuses on speed and efficiency. At the time of writing, gRPC cannot be used in web browsers, such as by the *Angular* or *React* framework, because browsers don’t provide the fine-grained access that gRPC requires to formulate its HTTP requests.