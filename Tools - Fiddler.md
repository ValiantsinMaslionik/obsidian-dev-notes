#tools 

---

### Autoresponder
[Autoresponder](https://docs.telerik.com/fiddler/knowledge-base/autoresponder)

### Request redirection

https://tpodolak.com/blog/2016/09/28/fiddler-redirecting-outgoing-calls-local-server/

As a back-end developer I am quite often contacted by front-end devs to take a look why certain UI calls to the server results in wrong data being returned by API. Usually I am provided with request payload so it is quite straightforward to reply it with Postman or Fiddler and see what is going on the backend. However for time to time, replying single request is not enough to reproduce the problem. In that case it is easier to just recreate the scenario on the UI and debug the API during that process. Unfortunately this approach requires backend devs to have UI up and running on their local machines and that is not always possible (e.g. you might not get access to UI repository). Fortunately with Fiddler it is quite easy to intercept outgoing front-end calls to the API and redirect them to your locally deployed version (so you can easily debug the API).

Let’s assume we would like to redirect all *simpletestspa.azurewebsites.net* calls to *localhost:50277*. 
In order to do that run Fiddler and 
- Go to Tools -> Hosts…
- Select “Enable remapping of requests for one host to a different host or IP, overriding DNS” checkbox
- Enter following values in the editor: *localhost:50277 simpletestspa.azurewebsites.net*

From now every outgoing request to *simpletestspa.azurewebsites.net* will be redirected to your local instance of the server on *localhost:50277*.

### Setup as system proxy

- Go to Tools -> WinINET Options:
	- 127.0.0.1:8888
	-  Capture traffic: On
