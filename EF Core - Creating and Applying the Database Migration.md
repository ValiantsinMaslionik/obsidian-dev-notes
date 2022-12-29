#EF_Core 

---

Entity Framework Core manages the relationship between data model classes and the database using a feature called *migrations*. When changes are made to the model classes, a new migration is created that modifies the database to match those changes. To create the initial migration, which will create a new database and prepare it to store `Calculation` objects, open a new PowerShell command prompt, navigate to the folder that contains the *Platform.csproj* file, and run the command shown in Listing 17-22.

Listing 17-22. Creating a Migration
```ps
dotnet ef migrations add Initial
```

The *dotnet ef* commands relate to Entity Framework Core. 
The command in Listing 17-22 creates a new migration named **Initial**, ==which is the name conventionally given to the first migration for a project==.

You will see that a *Migrations* folder has been added to the project and that it contains class files whose statements prepare the database so that it can store the objects in the data model. To apply the migration, run the command shown in Listing 17-23 in the Platform project folder.

Listing 17-23. Applying a Migration
```ps
dotnet ef database update
```

This command executes the commands in the migration created in Listing 17-22 and uses them to prepare the database, which you can see in the SQL statements written to the command prompt.