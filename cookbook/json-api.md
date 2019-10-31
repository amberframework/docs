# JSON API

This recipe will help you to setup a basic JSON API response in your application.

{% hint style="warning" %}
First you need an amber project generated with [Amber CLI](../guides/create-new-app.md) or [from scratch](from-scratch.md).
{% endhint %}

To create a JSON API from the command line, you simply need to use the [API generator](../cli/generate.md#api). Or for quick reference, run:

```bash
amber g api Post title:string entry:integer
```

And it will generate the following files:

```bash
04:09:54 Generate   | (INFO) Generating Amber::CLI::Api
04:09:54 Generate   | (INFO) new       spec/models/post_spec.cr
04:09:54 Generate   | (INFO) identical spec/models/spec_helper.cr
04:09:54 Generate   | (INFO) new       src/models/post.cr
04:09:54 Generate   | (INFO) new       db/migrations/20191031160954280_create_post.sql
Format ./config/routes.cr
04:09:54 Generate   | (INFO) new       spec/controllers/post_controller_spec.cr
04:09:54 Generate   | (INFO) identical spec/controllers/spec_helper.cr
04:09:54 Generate   | (INFO) new       src/controllers/post_controller.cr
```

This is a fully scaffolded JSON API.

### Custom

If you don't need full CRUD, you can also create a custom JSON API.

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
  pipeline :api do
    # pipelines...
  end

  routes :api do
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

For a full CRUD example file structure, see [JSON API full CRUD](../examples/json-api-full-crud.md).

Also see [Respond With](../guides/controllers/respond-with.md) and [Response & Request](../guides/controllers/request-and-response-objects.md).
