#EF_Core/Configuration

---

[[#Settings connection string]]

## Settings connection string
__appsettings.json__
```
{
	"Logging": {
		"LogLevel": {
			"Default": "Information",
			"Microsoft.AspNetCore": "Warning"
		}
	},
	"AllowedHosts": "*",
	"ConnectionStrings": {
		"SportsStoreConnection": "Server=(localdb)\\MSSQLLocalDB;Database=SportsStore;MultipleActiveResultSets=true"
	}
}
```
> Whole connection string must be placed on the single line!