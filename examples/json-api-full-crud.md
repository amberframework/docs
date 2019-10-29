
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
