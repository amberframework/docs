# Cookies

This recipe will help you to setup a cookie in your application.

{% hint style="warning" %}
First you need an amber project generated with [Amber CLI](../guides/create-new-app.md) or [from scratch](from-scratch.md).
{% endhint %}

{% code-tabs %}
{% code-tabs-item title="src/controllers/some\_controller.cr" %}
```ruby
class SomeController < ApplicationController
  def set_cookie
    cookies[:example] = {
      value: "a yummy cookie with amber color",
      http_only: true,
      secure: true
    }
    "Your example cookie has been cooked successfully!"
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
    get "/set_cookie", SomeController, :set_cookie
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Also see more detailed information about this in[ Cookies Guide](../guides/controllers/cookies.md).

