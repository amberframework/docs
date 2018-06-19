# JSON Mapping



{% hint style="warning" %}
First you need an amber project generated with [Amber CLI](../guides/create-new-app.md) or [from scratch](from-scratch.md).
{% endhint %}

This recipe will help you to setup a basic JSON Mapping in your application.

You can use `JSON.mapping` to directly create an object from JSON:

{% code-tabs %}
{% code-tabs-item title="src/controllers/some\_controller.cr" %}
```ruby
class User
  JSON.mapping(
    username: String,
    password: String,
  )
end

class SomeController < ApplicationController
  def json_mapping
    return "empty body" if response.body.to_s.blank?
    context.response.content_type = "application/json"
    User.from_json request.body.to_s
    User.name += "mapped!"
    User.to_json
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

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
    get "/json_mapping", SomeController, :json_mapping
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Alternatively you can use [`response_with`](../guides/controllers/respond-with.md) helper. Here you don't need to setup `content_type`, however the requested path requires a `.json` extension, by example `/json_mapping.json`

```ruby
class SomeController < ApplicationController
  def json_mapping
    return "empty body" if response.body.to_s.blank?
    User.from_json response.body.to_s
    User.name += "mapped!"
    response_with do
      json User.to_json
    end
  end
end
```

Also see [Response With](../guides/controllers/respond-with.md) and [Response & Request](../guides/controllers/request-and-response-objects.md).

