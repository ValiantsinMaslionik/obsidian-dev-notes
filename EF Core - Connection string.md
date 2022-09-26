#EF_Core/Configuration

---

## Settings connection string

__appsettings.json__

```json
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

>![[zICO - Warning - 16.png]] Whole connection string must be placed on the single line!