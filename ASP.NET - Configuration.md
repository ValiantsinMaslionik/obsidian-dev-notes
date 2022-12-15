#ASP_NET

---

## Configure default proxy

web.config
```xml
<defaultProxy>
	<proxy
		usesystemdefault="False"
		proxyaddress="http://127.0.0.1:8888"
		bypassonlocal="True"/>  
	<bypasslist>  
		<add address="[a-z]+\.contoso\.com$" />  
	</bypasslist>  
</defaultProxy>  
```