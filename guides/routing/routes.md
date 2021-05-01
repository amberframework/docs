# Routes
Routing in Amber provides developers a manifest to map application URLs to controller actions. By defining routes, you can separate how your application directs requests and how URLs are structured. Each route creates a real-time web socket handler, and define a series of pipeline transformations for scoping middleware to sets of routes.

A route connects a HTTP request to a function inside a controller. When your Amber application receives an incoming request for: `GET /users/24` it asks the Amber router to find the corresponding controller action to direct the request towards. If the router finds a match, for example `get users/:id, UsersController, :index`, the request will be dispatched to the matching method with the provided parameters, in this case the UsersController.index action with { id: 24 } in the params hash.

## Configuring Routes

Routes are configured in the `{project_name}/config/routes.cr` file.

```ruby
Amber::Server.configure do |app|
  routes :static do
    # Each route is defined as follow
    # verb resource : String, controller : Symbol, action : Symbol
    get "/*", StaticController, :index
  end
end
```

## Defining Routes

The **routes** macro accepts a **pipeline** name and a **scope**. In addition to the pipeline and scope, the routes macro also takes an additional block parameter where you can define routes. All routes defined within a scope's block will make use of the provided pipeline and the URLs will be scoped accordingly.

For example, let's say you are creating a static website and you want the URL to be displayed as `http://www.mycoolsite.com/page`. To do this, you would setup your routes as follows:

```ruby
# routes(pipeline, scope)
routes :web, "/page"
```

If you wanted to created a namespace for nesting your URL routes, you can use the scope parameter to do so.

```ruby
routes :web, '/v1' do
  get "/about", StaticController, :about
end
```

Mapping the above route

| Http Method | Path | Controller | Action |
| :--- | :--- | :--- | :--- |
| get | "/v1/about" | StaticController | :about |

Your controller action will need to return a string or render a view. If no string or view is rendered your routes configuration will raise an error during compilation.

```ruby
class StaticController < ApplicationController
	 def about
		 "About my cool page!"
	 end

	 # or:

	 def about
		 render("about.ecr")
	 end
end
```

## Resources

The router supports other macros besides those for HTTP verbs like _get_, _post_, and _put_. The most important among them is `resources`. The `resources` macro is a quick way to setup up resourceful routing for all seven standard actions for a controller in a single line. 

{% hint style="info" %}
In order to use resourceful routing for a particular controller, your controller _must_ define and implement all seven standard actions: `index`, `edit`, `new`, `show`, `create`, `destroy`, and `update`. If your controller does not implement all seven actions, an error will be raised during compilation.
{% endhint %}

Let’s add a resource to the `config/routes.cr`

```ruby
routes :web do
  resources "/posts", PostsController
end
```

Then go to the root of your project, and run `amber routes`

This will output the standard matrix of HTTP verbs, controller, action, pipeline, scope, and URI pattern.

![Amber Routes Matrix Example](https://raw.githubusercontent.com/amberframework/site-assets/master/images/amber_routes.png)

## Scoped Routes

Scopes are a way to group routes under a common path prefix and scoped set of pipeline handlers. We might want to do this for admin functionality, APIs, and especially for versioned APIs. Let’s say we have user-generated posts on a site, and that those posts first need to be approved by an admin. The semantics of these resources are quite different, and they might not share the same controller. Scopes enable us to segregate these routes.

The paths to the user facing reviews would look like a standard resource.

```text
/posts
/posts/1234
/posts/1234/edit
...
```

But for the admin console paths could be prefixed with /admin.

```text
/admin/posts
/admin/posts/1234
/admin/posts/1234/edit
...
```

We accomplish this with a scoped route that sets a path option to /admin like this one. For now, let’s not nest this scope inside of any other scopes \(like the scope "/", HelloWeb provides in a new app\).

```ruby
# Not Scoped
routes :web do
  resources "/posts", PostsController
end

# Scoped
routes :web, "/admin" do
  resources "/posts", AdminPostsController
end
```

### Excluding and Including Actions

Sometimes you want to use `resources` as a shortcut for defining routes, and with that you don't want to define routes for actions that don't exist yet. `Resources` allow you to pass another argument, `only:` or `except:` to either include actions or exclude them from being generated.

This will define the following routes:

```ruby
resources "/user", UserController, only: [:index, :show]
resources "/user", UserController, except: [:index, :show]
```

