# Routing

Routing provides you tools that map URLs to controller actions. By defining routes, you can separate how your application is implemented from how its URL’s are structured, Routes wire up real-time web socket handlers, and define a series of pipeline transformations for scoping middleware to sets of routes.

### Pipelines

Every Amber application needs to define a pipeline with a set of pipes each pipeline allow a set of middleware transformations to be applied to different sets of route, this give you granular control and explicitness of which transformation to run for each of the app requests.

```ruby
# All api scoped routes will run these transformations
pipeline :web do
  # Plug is the method to use connect a pipe (middleware)
  # A plug accepts an instance of HTTP::Handler
  plug Amber::Pipe::Logger.new
  plug HTTP::StaticFileHandler.new "../examples/public", false
  plug HTTP::CompressHandler.new
end
```

Above we define a pipeline called `:web` as you can see this `:web` pipeline transforms the request using a _Logger_, _StaticFileHandler_ and a _CompressHandler_, all of which extends from the Crystal [_HTTP::Handler_](https://crystal-lang.org/api/0.22.0/HTTP/Handler.html) class, A handler is a class which includes [`HTTP::Handler`](https://crystal-lang.org/api/0.22.0/HTTP/Handler.html) and implements the [`#call`](https://crystal-lang.org/api/0.22.0/HTTP/Handler.html#call%28context%3AHTTP%3A%3AServer%3A%3AContext%29-instance-method) method. You can use a handler to intercept any incoming request and can modify the response. These can be used for request throttling, ip-based whitelisting, adding custom headers e.g.

With our `:web` pipeline now define we can use it in our routes.

### Routes

A route connects a HTTP request to an action inside a controller. When your Amber application receives an incoming request for:  `GET /users/24` it asks the Amber router to match it to a controller action. it the router finds a match `get users/:id, UsersController, :index` the request is dispatched to the UsersController.index action with { id: 24 } in the params hash.

The **routes** macro accepts a pipeline name and a scope. Lets say you are defining a static website and you want your URL to be displayed as `http://www.mycoolsite.com/page/about` you will define your routes as:

```ruby
routes :web, '/static' do
  get "/about", StaticController, :about
end
```



