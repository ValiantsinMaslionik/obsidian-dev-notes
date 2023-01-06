#aspnet_core/Routing

---

Each middleware component decides whether to act on a request as it passes along the pipeline. Some
components are looking for a specific header or query string value, but most components—especially
terminal and short-circuiting components—are trying to match URLs.

Each middleware component has to repeat the same set of steps as the request works its way along the
pipeline. You can see this in the middleware defined in the previous section, where both components go
through the same process: split up the URL, check the number of parts, inspect the first part, and so on.

This approach is far from ideal. It is inefficient because the same set of operations is repeated to process
the URL. It is difficult to maintain because the URL that each component is looking for is hidden in its code.
It breaks easily because changes must be carefully worked through in multiple places. For example, the
Capital component redirects requests to a URL whose path starts with /population, which is handled by
the Population component. If the Population component is revised to support the /size URL instead, then
this change must also be reflected in the Capital component. Real applications can support complex sets of
URLs and working changes fully through individual middleware components can be difficult.

URL routing solves these problems by introducing middleware that takes care of matching request
URLs so that components, called endpoints, can focus on responses. The mapping between endpoints and
the URLs they require is expressed in a route. The routing middleware processes the URL, inspects the set of
routes, and finds the endpoint to handle the request, a process known as #Term/routing.
