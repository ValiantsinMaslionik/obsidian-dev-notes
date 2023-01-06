#aspnet_core 

---

Services are objects that provide features in a web application. Any class can be used as a service, and
there are no restrictions on the features that services provide. What makes services special is that they are
managed by ASP.NET Core, and a feature called dependency injection makes it possible to easily access
services anywhere in the application, including middleware components.
Dependency injection can be a difficult topic to understand, and I describe it in detail in Chapter 14.
For now, it is enough to know that there are objects that are managed by the ASP.NET Core platform that can
be shared by middleware components, either to coordinate between components or to avoid duplicating
common features, such as logging or loading configuration data, as shown in Figure 12-3.

![[zIMG - ASP.NET - services.png]]
Figure 12-3. Services in the ASP.NET Core platform

As the figure shows, middleware components use only the services they require to do their work. As
you will learn in later chapters, ASP.NET Core provides some basic services that can be supplemented by
additional services that are specific to an application.