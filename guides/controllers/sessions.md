# Sessions

## The Session

Amber uses an Amber:Router::Session::Store to manage session storage. This store is most suitable when your sessions don't hold critical data and don't need to persist for long periods.

The default session store in Amber relies on cookies, offering significantly faster performance compared to other options. It automatically configures the cookie store based on your application's settings.

Sessions typically carry minimal data, such as a user\_id and flash message, fitting within the 4K cookie size limit. If you attempt to store more than 4K of data, it triggers a CookieOverflow exception.

To configure the session:

```ruby
#  Cookie Store
Amber.settings.session = {
  :key     => "name.session",
  :store   => "cookie",
  :expires => 120, 
  # :expires => 0,
  # would make the session last as long as the browser is open;
  # upon closing the browser, the session would terminate.
  :secret  => "secret"
}

# Redis Store
Amber.settings.session = {
  :key       => "name.session",
  :store     => "redis",
  :expires   => 120,
  :secret    => "secret",
  :redis_url => "redis://localhost:6379",
}
```

Also, include the Session into the Pipeline:

```ruby
# Keep in mind the order of the Pipes. Session hash needs to be populated before 
# trying to access the session flash scope, the flash depends on the session. 
pipeline :web do
  plug Amber::Pipe::Session.new
  plug Amber::Pipe::Logger.new
  plug Amber::Pipe::Flash.new
  plug Amber::Pipe::CSRF.new
end
```

To access the session:

```ruby
class ApplicationController < Amber::Controller::Base
  # Finds the User with the ID stored in the session with the key
  # :current_user_id This is a common way to handle user login in
  # an Amber application; logging in sets the session value and
  # logging out removes it.
  private def current_user
    @_current_user ||= session[:current_user_id] &&
      User.find_by(id: session[:current_user_id])
  end
end
```

To store something in the session, just assign it to the key like a hash:

```ruby
class LoginsController < ApplicationController
  # "Create" a login, aka "log the user in"
  def create
    if user = User.authenticate(params[:username], params[:password])
      # Save the user ID in the session so it can be used in
      # subsequent requests
      session[:current_user_id] = user.id
      redirect_to root_url
    end
  end
end
```

To remove something from the session, use `session.delete(key)`:

```ruby
class LoginsController < ApplicationController
  # "Delete" a login, aka "log the user out"
  def destroy
    # Remove the user id from the session
    @_current_user = nil
    session.delete(:current_user_id)
    redirect_to root_url
  end
end
```

### The Flash

The flash is a special part of the session that resets after each request. This means any stored values are only available for the next request. It's handy for passing error messages or similar information.

To access the flash, you treat it like a hash, much like accessing the session itself.

Let's take logging out as an example. The controller can send a message that will be shown to the user on their next request:

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

Rendering the flash message:

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

If you want a flash value to be carried over to another request, use the keep method:

```ruby
class MainController < ApplicationController
  # Let's say this action corresponds to root_url, but you want
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

#### Flash.now

By default, adding values to the flash will make them available to the next request, but sometimes you may want to access those values in the same request. For example, if the create action fails to save a resource and you render the new template directly, that's not going to result in a new request, but you may still want to display a message using the flash. To do this, you can use flash.now in the same way you use the normal flash.

```ruby
class ClientsController < ApplicationController
  def create
    client = Client.new(params[:client])
    if client.save
      # ...
    else
      flash.now[:error] = "Could not save client"
      render action: "new"
    end
  end
end
```

Make sure the Flash, Session and CSRF pipelines are enabled in your `routes.cr` file and in the order that the scaffolding renders them.

## CSRF

To use CSRF, enable the pipe in your `routes.cr`

Then, insert the `csrf_tag` helper in your forms.

### How to use CSRF with Ajax

To use CSRF with Aljax, simply call the `csrf_tag` helper inside your controller and return it as part of a JSON object:

```ruby
def my_action
    {csrf: csrf_tag}.to_json
end
```

In your Javascript, after getting the JSON object back, refresh your CSRF tag with the one from the server.

```javascript
$("input[name*=_csrf]").replaceWith(e['csrf']);
```
