# Amber Routing

The Amber router recognizes URLs and dispatches them to a controller's action. It can also generate paths and URLs, avoiding the need to hardcode strings in your views.

## [Pipelines](/routing/pipelines.md)

A _pipeline_ is a set of of transformations that is performed to a HTTP request. These transformations come in the form of a pipe. A _pipe_ is a** **class which includes [`HTTP::Handler`](https://crystal-lang.org/api/0.22.0/HTTP/Handler.html) and implements the [`#call`](https://crystal-lang.org/api/0.22.0/HTTP/Handler.html#call%28context%3AHTTP%3A%3AServer%3A%3AContext%29-instance-method) method. You can use a handler to intercept any incoming request and modify the response. These can be used for request throttling, ip-based whitelisting, adding custom headers.

## [Routes](/getting-started/routing/routes.md)

Routing provides you tools that map URLs to controller actions. By defining routes, you can separate how your application is implemented from how its URL’s are structured, Routes wire up real-time web socket handlers, and define a series of pipeline transformations for scoping middleware to sets of routes.



