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

Alternatively you can use [`response_with`](../guides/controllers/respond-with.md) helper. Here you don't need to setup `content_type`, however the requested path requires a `.json` extension, by example `/json_api.json`

```ruby
class SomeController < ApplicationController
  def json_api
    data = {name: "Amber", age: 1}
    response_with do
      json data.to_json
    end
  end
end
```

Also see [Response With](../guides/controllers/respond-with.md) and [Response & Request](../guides/controllers/request-and-response-objects.md).

