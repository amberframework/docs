# Routing

The Amber router recognizes URLs and dispatches them to a controller's action. It can also generate paths and URLs, avoiding the need to hardcode strings in your views.

## Pipelines

A _pipeline_ is a set of of transformations that is performed on an HTTP request. These transformations come in the form of a pipe. A _pipe_ is a _\*\*_class which includes [`HTTP::Handler`](https://crystal-lang.org/api/latest/HTTP/Handler.html) and implements the [`#call`](https://crystal-lang.org/api/latest/HTTP/Handler.html#call%28context%3AHTTP%3A%3AServer%3A%3AContext%29-instance-method) method. You can think of a pipe as a form of middleware that intercepts any incoming request and modifies the response. Some example usages for pipes are request throttling, ip-based whitelisting, adding custom headers.

{% page-ref page="pipelines.md" %}

## Routes

Routing in Amber provides developers a manifest to map application URLs to controller actions. By defining routes, you can separate how your application directs requests and how URLs are structured. Each route creates a real-time web socket handler, and define a series of pipeline transformations for scoping middleware to sets of routes.

{% page-ref page="routes.md" %}

