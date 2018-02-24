# Routes

Routing provides you tools that map URLs to controller actions. By defining routes, you can separate how your application is implemented from how its URL’s are structured. Routes wire up real-time web socket handlers, and define a series of pipeline transformations for scoping middleware to sets of routes.

A route connects a HTTP request to an action inside a controller. When your Amber application receives an incoming request for:  `GET /users/24` it asks the Amber router to match it to a controller action. If the router finds a match `get users/:id, UsersController, :index` the request is dispatched to the UsersController.index action with { id: 24 } in the params hash.

### Configuring Routes

Routes are configured in the `{project_name}/config/routes.cr` file.

```crystal
Amber::Server.configure do |app|
  routes :static do
    # Each route is defined as follow
    # verb resource : String, controller : Symbol, action : Symbol
    get "/*", StaticController, :index
  end
end
```

### Defining Routes

The **routes** macro accepts a **pipeline** name and a **scope**, in which all routes defined within this block will make use of the pipeline and the url will be scoped.

Lets say you are defining a static website and you want the URL to be displayed as `http://www.mycoolsite.com/page`. You will define your routes as:

```crystal
# routes(pipeline, scope)
routes :web, "/page"
```

The routes macro takes a last argument. And a block within the block is where you define your routes.

```crystal
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

## Resources

The router supports other macros besides those for HTTP verbs like _get_, _post_, and _put_. The most important among them is `resources`, which expands out to eight endpoints.

Let’s add a resource to the `config/routes.cr`

```crystal
routes :web do
  resources "/posts", PostsController
end
```

Then go to the root of your project, and run `amber routes`

It outputs the standard matrix of HTTP verbs, controller, action, pipeline, scope, and URI pattern.

```shell
○ amber routes
╔════════╦══════════════════╦═════════╦══════════╦═══════╦═════════════════╗
║ Verb   | Controller       | Action  | Pipeline | Scope | URI Pattern     ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼─────────────────╣
║ GET    | PostController   | index   | web      |       | /posts          ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼─────────────────╣
║ GET    | PostController   | show    | web      |       | /posts/:id      ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼─────────────────╣
║ GET    | PostController   | new     | web      |       | /posts/new      ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼─────────────────╣
║ GET    | PostController   | edit    | web      |       | /posts/:id/edit ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼─────────────────╣
║ POST   | PostController   | create  | web      |       | /posts          ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼─────────────────╣
║ PATCH  | PostController   | update  | web      |       | /posts/:id      ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼─────────────────╣
║ PUT    | PostController   | update  | web      |       | /posts/:id      ║
╠────────┼──────────────────┼─────────┼──────────┼───────┼─────────────────╣
║ DELETE | PostController   | destroy | web      |       | /posts/:id      ║
╚════════╩══════════════════╩═════════╩══════════╩═══════╩═════════════════╝
```

## Scoped Routes

Scopes are a way to group routes under a common path prefix and scoped set of pipeline handlers. We might want to do this for admin functionality, APIs, and especially for versioned APIs. Let’s say we have user-generated posts on a site, and that those posts first need to be approved by an admin. The semantics of these resources are quite different, and they might not share the same controller. Scopes enable us to segregate these routes.

The paths to the user facing reviews would look like a standard resource.

```shell
/posts
/posts/1234
/posts/1234/edit
...
```

But for the admin console paths could be prefixed with /admin.

```shell
/admin/posts
/admin/posts/1234
/admin/posts/1234/edit
...
```

We accomplish this with a scoped route that sets a path option to /admin like this one. For now, let’s not nest this scope inside of any other scopes (like the scope "/", HelloWeb provides in a new app).

```crystal
# Not Scoped
routes :web do
  resources "/posts", PostsController
end

# Scoped
routes :web, "/admin" do
  resources "/posts", AdminPostsController
end
```

Running `amber routes` again we get the following:

```shell
 ○ amber routes
╔════════╦═════════════════════╦═════════╦══════════╦════════╦═══════════════════════╗
║ Verb   | Controller          | Action  | Pipeline | Scope  | URI Pattern           ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ get    | PostsController     | index   | web      |        | /posts                ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ get    | PostsController     | show    | web      |        | /posts/:id            ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ get    | PostsController     | new     | web      |        | /posts/new            ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ get    | PostsController     | edit    | web      |        | /posts/:id/edit       ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ post   | PostsController     | create  | web      |        | /posts                ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ patch  | PostsController     | update  | web      |        | /posts/:id            ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ put    | PostsController     | update  | web      |        | /posts/:id            ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ delete | PostsController     | destroy | web      |        | /posts/:id            ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ get    | AdminPostController | index   | web      | /admin | /admin/posts          ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ get    | AdminPostController | show    | web      | /admin | /admin/posts/:id      ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ get    | AdminPostController | new     | web      | /admin | /admin/posts/new      ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ get    | AdminPostController | edit    | web      | /admin | /admin/posts/:id/edit ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ post   | AdminPostController | create  | web      | /admin | /admin/posts          ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ patch  | AdminPostController | update  | web      | /admin | /admin/posts/:id      ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ put    | AdminPostController | update  | web      | /admin | /admin/posts/:id      ║
╠────────┼─────────────────────┼─────────┼──────────┼────────┼───────────────────────╣
║ delete | AdminPostController | destroy | web      | /admin | /admin/posts/:id      ║
╚════════╩═════════════════════╩═════════╩══════════╩════════╩═══════════════════════╝
```

#### Excluding and Including Actions

Sometimes you want to use `resources` as a shortcut for defining routes, and with that you don't want to define routes for actions that don't exist yet. `Resources` allow you to pass another argument, `only:` or `except:` to either include actions or exclude them from being generated.

This will define the following routes:

```
resources "/user", UserController, only: [:index, :show]
resources "/user", UserController, except: [:index, :show]
```

