# Authenticate

This recipe will help you to setup an authentication pipe in your application:

{% hint style="warning" %}
First you need an amber project generated with [Amber CLI](../guides/create-new-app.md) or [from scratch](from-scratch.md).
{% endhint %}

{% code-tabs %}
{% code-tabs-item title="src/pipes/authenticate.cr" %}
```ruby
class Authenticate < Amber::Pipe::Base
  PUBLIC_PATHS = ["/"]

  def call(context)
    some_id = context.session["some_id"]? # setup by some controller
    if some_id || public_path?(context.request.path)
      call_next(context)
    else
      context.flash[:warning] = "Please Sign In"
      context.response.headers.add "Location", "/"
      context.response.status_code = 302
    end
  end

  private def public_path?(path)
    PUBLIC_PATHS.includes?(path)

    # Different strategies can be used to determine if a path is public
    # Example, if /admin/* paths are the only private paths
    # return false if path.starts_with?("/admin")
    #
    # Example, if only a few private paths exist
    # return false if ["/secret", "/super/secret"].includes?(path)
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
    # other pipes...
    plug Authenticate.new
  end

  routes :web do
    # some routes,,,
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

To have a full authentication experience some extra controllers, views and models are still required, please see [Amber Auth Example](../examples/amber-auth.md).

Also see [pipelines](../guides/routing/pipelines.md).

