### Pipelines

A _pipeline_ is a set of of transformations that is performed to a HTTP request. These transformations come in the form of a pipe. A _pipe_ is a** **class which includes [`HTTP::Handler`](https://crystal-lang.org/api/0.22.0/HTTP/Handler.html) and implements the [`#call`](https://crystal-lang.org/api/0.22.0/HTTP/Handler.html#call%28context%3AHTTP%3A%3AServer%3A%3AContext%29-instance-method) method. You can use a handler to intercept any incoming request and modify the response. These can be used for request throttling, ip-based whitelisting, adding custom headers.

Every Amber application needs to define a _pipeline_ with a set of _pipes_ each pipeline allow a set of transformations to be applied to different sets of route, this give you granular control and explicitness of which transformation to run for each of the requests.

```crystal
pipeline :web do
  plug Amber::Pipe::Logger.new
  plug HTTP::StaticFileHandler.new "../examples/public", false
  plug HTTP::CompressHandler.new
end
```

Above we define a pipeline called `:web` as you can see the `:web` pipeline transforms the request using a _Logger_, _StaticFileHandler_ and a _CompressHandler_, all of which extends from the Crystal [_HTTP::Handler_](https://crystal-lang.org/api/0.22.0/HTTP/Handler.html) class.

Amber provides us some default pipes for a number of common tasks. In turn we can customize them as well as create new pipelines to meet our needs.

* **Pipe::Static** serves static assets

* **Pipe::Logger** logs incoming requests

* **Pipe::CodeReload** enables code reloading for all entries in the web directory.

* **Pipe::CORS** Handler adds support for Cross Origin Resource Sharing.

* **Pipe::CSRF** Handler adds support for Cross Site Request Forgery.

* **Pipe::Error** Handler catches RouteNotFound and returns.

* **Pipe::Flash** handler provides a mechanism to pass flash message between

* **Pipe::Session** a plug that sets up session management.

With our `:web` pipeline now define we can use it in our routes difinitions.

