#NET/LINQ

---

The _IQueryable&gt;T&lt;_ interface is useful because it allows a collection of objects to be queried
efficiently. Later in this chapter, I add support for retrieving a subset of Product objects from a
database, and using the IQueryable&gt;T&lt; interface allows me to ask the database for just the objects
that I require using standard LINQ statements and without needing to know what database server stores
the data or how it processes the query. Without the IQueryable&gt;T&lt; interface, I would have to retrieve
all of the Product objects from the database and then discard the ones that I don’t want, which
becomes an expensive operation as the amount of data used by an application increases. It is for this
reason that the IQueryable&gt;T&lt; interface is typically used instead of IEnumerable&gt;T&lt; in database
repository interfaces and classes.