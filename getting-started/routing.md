# Routing

Routing provides you tools that map URLs to controller actions. By defining routes, you can separate how your application is implemented from how its URL’s are structured, Routes wire up real-time web socket handlers, and define a series of pipeline transformations for scoping middleware to sets of routes.

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

Amber provides us some default pipelines for a number of common tasks. In turn we can customize them as well as create new pipelines to meet our needs.

* **Pipe::Static** serves static assets

* **Pipe::Logger** logs incoming requests

* **Pipe::CodeReload** enables code reloading for all entries in the web directory.

* **Pipe::CORS** Handler adds support for Cross Origin Resource Sharing.

* **Pipe::CSRF** Handler adds support for Cross Site Request Forgery.

* **Pipe::Error** Handler catches RouteNotFound and returns.

* **Pipe::Flash** handler provides a mechanism to pass flash message between

* **Pipe::Session** a plug that sets up session management.

With our `:web` pipeline now define we can use it in our routes dinfitions

### Routes

A route connects a HTTP request to an action inside a controller. When your Amber application receives an incoming request for:  `GET /users/24` it asks the Amber router to match it to a controller action. it the router finds a match `get users/:id, UsersController, :index` the request is dispatched to the UsersController.index action with { id: 24 } in the params hash.

The **routes** macro accepts a _pipeline_ name and a \_scope, \_in which all routes define within this block will make use of the pipeline and the url will be scoped.

Lets say you are defining a static website and you want all your URL to be displayed as `http://www.mycoolsite.com/page` you will define your routes as:

```ruby
# routes(pipeline, scope)
routes :web, "/page"
```

The routes macro takes a last argument, a \_block, \_within the block is where you define your routes.

```ruby
routes :web, '/static' do
  get "/about", StaticController, :about
end
```

Mapping the above route

| Http Method | Resource | Controller | Action |
| :--- | :--- | :--- | :--- |
| get | "/about" | StaticController | :about |



