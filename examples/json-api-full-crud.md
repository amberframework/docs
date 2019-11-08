
# JSON API full CRUD

If you want to configure a full crud JSON API, you will need to first generate a model with the following command (for this example we will use the model `Post` with `title` and `body` attributes):

```
amber generate model Post title body
```

This will generate a database migration and a `Post` model file in `models/post.cr`.

{% code-tabs %}
{% code-tabs-item title="src/models/post.cr" %}
```ruby
class Post < Granite::Base
  connection pg
  table posts

  column id : Int64, primary: true
  column title : String?
  column body : String?
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Then in your controller:

{% code-tabs %}
{% code-tabs-item title="src/controllers/post_controller.cr" %}
```ruby
class PostController < ApplicationController
  def index
    posts = Post.all
    respond_with 200 do
      json posts.to_json
    end
  end

  def show
    if post = Post.find params["id"]
      respond_with 200 do
        json post.to_json
      end
    else
      results = {status: "not found"}
      respond_with 404 do
        json results.to_json
      end
    end
  end

  def create
    post = Post.new(post_params.validate!)

    if post.valid? && post.save
      respond_with 201 do
        json post.to_json
      end
    else
      results = {status: "invalid"}
      respond_with 422 do
        json results.to_json
      end
    end
  end

  def update
    if post = Post.find(params["id"])
      post.set_attributes(post_params.validate!)
      if post.valid? && post.save
        respond_with 200 do
          json post.to_json
        end
      else
        results = {status: "invalid"}
        respond_with 422 do
          json results.to_json
        end
      end
    else
      results = {status: "not found"}
      respond_with 404 do
        json results.to_json
      end
    end
  end

  def destroy
    if post = Post.find params["id"]
      post.destroy
      respond_with 204 do
        json ""
      end
    else
      results = {status: "not found"}
      respond_with 404 do
        json results.to_json
      end
    end
  end

  def post_params
    params.validation do
      required(:title, msg: nil, allow_blank: true)
      required(:body, msg: nil, allow_blank: true)
    end
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
  pipeline :api do
    # pipelines...
  end

  routes :api do
    # other routes,,,
    resources "/posts", PostController, except: [:edit, :new]
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}
