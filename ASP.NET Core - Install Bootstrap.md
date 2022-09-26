#ASP_NET_CORE/Bootstrap

---

## Installing the Bootstrap CSS Framework

`libman install bootstrap@5.1.3 -d wwwroot/lib/bootstrap`

> The -d argument specifies the location into which the package is installed.

## Applying Bootstrap Classes

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<link href="/lib/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
	</head>
	<body>
		<h3 class="bg-primary text-white text-center p-2">New Message</h3>
	</body>
</html>
```