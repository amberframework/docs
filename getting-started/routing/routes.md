# Routes

Routing provides you tools that map URLs to controller actions. By defining routes, you can separate how your application is implemented from how its URL’s are structured, Routes wire up real-time web socket handlers, and define a series of pipeline transformations for scoping middleware to sets of routes.

```ruby
Amber::Server.instance.config do |app|
  pipeline :web do
    # Plug is the method to use connect a pipe (middleware)
    # A plug accepts an instance of HTTP::Handler
    # plug Amber::Pipe::Params.new
    plug Amber::Pipe::Logger.new
    plug Amber::Pipe::Flash.new
    plug Amber::Pipe::Session.new
    plug Amber::Pipe::CSRF.new
  end

  # All static content will run these transformations
  pipeline :static do
    plug HTTP::StaticFileHandler.new("./public")
    plug HTTP::CompressHandler.new
  end

  routes :static do
    # Each route is defined as follow
    # verb resource : String, controller : Symbol, action : Symbol
    get "/*", StaticController, :index
  end

  routes :web do
    # You can also define all resources at once with the resources macro.
    # This will define the following routes: 
    # resources path, controller, actions
    # resources "/user", UserController, only: [:index, :show]
    # resources "/user", UserController, except: [:index, :show]
    #
    # GET     /users          UserController  :index
    # GET     /users/:id/edit UserController  :edit
    # GET     /users/new      UserController  :new
    # GET     /users/:id      UserController  :show
    # POST    /users          UserController  :create
    # PATCH   /users/:id      UserController  :update
    # PUT     /users/:id      UserController  :update
    # DELETE  /users/:id      UserController  :delete
    get "/", HomeController, :index
  end

  routes :api, "/api" do
    get "/", ApiController, :index
  end

end
```

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

Your action will need to return a string or render a view or this will cause your routes to throw an error during compilation.

```crystal
def about
  "About my cool page!"
end

# or:

def about
  render("about.ecr")
end

```



