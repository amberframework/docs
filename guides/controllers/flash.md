# Flash

The flash is a special part of the session which is cleared with each request. This means that values stored there will only be available on the next request, which is useful for passing error messages etc.

## Accessing the flash scope

It is accessed in much the same way as the session, as a hash.

Let's use the act of logging out as an example. The controller can send a message which will be displayed to the user on the next request:

```ruby
class LoginsController < ApplicationController
  def destroy
    session[:current_user_id] = nil
    #  Alternatively, `flash.notice=` could be use.
    flash[:notice] = "You have successfully logged out."
    redirect_to root_url
  end
end
```

## Rendering the flash message

```markup
<html>
  <!-- <head/> -->
  <body>
    <% flash.each do |name, msg| -%>
      <%= content_tag :div, msg, class: name %>
    <% end -%>

    <!-- more content -->
  </body>
</html>
```

## Flash values

You can pass anything that the session can store; you're not limited to notices and alerts:

```markup
<% if flash[:just_signed_up] %>
  <p class="welcome">Welcome to our site!</p>
<% end %>
```

## Flash.keep

If you want a flash value to be carried over to another request, use the keep method:

```ruby
class MainController < ApplicationController

  # Let's say this action corresponds to root URL, but you want
  # all requests here to be redirected to UsersController#index.
  # If an action sets the flash and redirects here, the values
  # would normally be lost when another redirect happens, but you
  # can use 'keep' to make it persist for another request.

  def index
    # Will persist all flash values.
    flash.keep

    # You can also use a key to keep only some kind of value.
    # flash.keep(:notice)
    redirect_to users_url
  end
end
```

## Flash.now

By default, adding values to the flash will make them available to the next request, but sometimes you may want to access those values in the same request. For example, if the create action fails to save a resource and you render the new template directly, that's not going to result in a new request, but you may still want to display a message using the flash. To do this, you can use flash.now in the same way you use the normal flash.

```ruby
class ClientsController < ApplicationController
  def create
    @client = Client.new(params[:client])
    if @client.save
      # ...
    else
      flash.now[:error] = "Could not save client"
      render action: "new"
    end
  end
end
```

