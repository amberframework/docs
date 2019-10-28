# JSON API

This recipe will help you to setup a basic JSON API response in your application.

{% hint style="warning" %}
First you need an amber project generated with [Amber CLI](../guides/create-new-app.md) or [from scratch](from-scratch.md).
{% endhint %}

```ruby
class SomeController < ApplicationController
  def json_api
    # You can easily access the context
    # and set content_type like 'application/json'.
    # Look how easy to build a JSON serving API.
    context.response.content_type = "application/json"
    data = {name: "Amber", age: 1}
    data.to_json
  end
end
```

Then in your routes file:

{% code-tabs %}
{% code-tabs-item title="config/routes.cr" %}
```ruby
Amber::Server.configure do |app|
  pipeline :web do
    # pipelines...
  end

  routes :web do
    # other routes,,,
    get "/json_api", SomeController, :json_api
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Alternatively you can use [`respond_with`](../guides/controllers/respond-with.md) helper. Here you don't need to setup `content_type`, however the requested path requires a `.json` extension, by example `/json_api.json`

```ruby
class SomeController < ApplicationController
  def json_api
    data = {name: "Amber", age: 1}
    respond_with do
      json data.to_json
    end
  end
end
```

# Full CRUD JSON API

If you want to configure a full crud JSON API, you will need to generate a model with the following command:

```
amber generate model Post title body
```

This will generate a migration and a `Post` model in `models/post.cr` to contain the following:

{% code-tabs %}
{% code-tabs-item title="src/models/post.cr" %}
```ruby
class Post < Granite::Base
  connection pg
  table posts

  column id : Int64, primary: true
  column title : String?
  column body : String?
  timestamps
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Then in your controller:

{% code-tabs %}
{% code-tabs-item title="src/controllers/postcontroller.cr" %}
```ruby
class PostController < ApplicationController
  getter post = Post.new

  before_action do
    only [:show, :update, :destroy] { set_post }
  end

  def index
    posts = Post.all
    respond_with do
     json posts.to_json
    end
  end

  def show
    respond_with do
     json posts.to_json
    end
  end

  def create
    post = Post.new post_params.validate!
    error_msg = { "error" => "failed to create" }

    if post.save
      respond_with do
        json post.to_json
      end
    else
      respond_with do
        json error_msg.to_json
      end
    end
  end

  def update
    error_msg = { "error" => "failed to update" }

    if post.update(post_params.validate!)
      respond_with do
        json post.to_json
      end
    else
      respond_with do
        json error_msg.to_json
      end
    end
  end

  def destroy
    error_msg = { "error" => "failed to delete" }
    if post.destroy
      respond_with do
        json post.to_json
      end
    else
      respond_with do
        json error_msg.to_json
      end
    end
  end

  private def post_params
    params.validation do
      required :title { |f| !f.nil? }
      required :body { |f| !f.nil? }
    end
  end

  private def set_post
    @post = Post.find! params[:id]
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

And your routes file:

{% code-tabs %}
{% code-tabs-item title="config/routes.cr" %}
```ruby
Amber::Server.configure do |app|
  pipeline :web do
    # pipelines...
  end

  routes :web do
    # other routes,,,
    resources "/posts", SomeController, except: [:edit, :new]
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Also see [Respond With](../guides/controllers/respond-with.md) and [Response & Request](../guides/controllers/request-and-response-objects.md).
