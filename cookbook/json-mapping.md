# JSON Mapping

This recipe will help you to setup a basic JSON Mapping in your application.

{% hint style="warning" %}
First you need an amber project generated with [Amber CLI](../guides/create-new-app.md) or [from scratch](from-scratch.md).
{% endhint %}

JSON requests are automatically parsed into the `params` macro when the `accept` header is present and with `application/json` &#x20;

You can use this in combination with the [`respond_with`](../guides/controllers/respond-with.md) helper. Here you don't need to setup `content_type`, however, the requested path requires a `.json` extension, by example `/json_mapping.json`

```ruby
class SomeController < ApplicationController
  def json_mapping
    return "empty body" if params["some_json_key_from_your_request"]
    user = User.from_json request.body.to_s
    user.username += "mapped!"
    response_with do
      json user.to_json
    end
  end
end
```

Also see [Response With](../guides/controllers/respond-with.md) and [Response & Request](../guides/controllers/request-and-response-objects.md).
