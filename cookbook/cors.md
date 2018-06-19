# CORS

{% hint style="warning" %}
First you need an amber project generated with [Amber CLI](../guides/create-new-app.md) or [from scratch](from-scratch.md).
{% endhint %}

This recipe will help you to setup a [Cross-Origin Resource Sharing](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) pipe in your application.

{% code-tabs %}
{% code-tabs-item title="config/application.cr" %}
```ruby
Amber::Server.configure do
  pipeline :api do
    plug Pipe::CORS.new
  end
  
  routes :api do
    # your routes here...
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Also see [pipelines](../guides/routing/pipelines.md).

