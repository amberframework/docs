# The Session

A session store that uses an Amber:Router::Session::Store to store the sessions. This store is most useful if you don't store critical data in your sessions and you don't need them to live for extended periods of time.

This cookie-based session store is the Amber default. It is dramatically faster than the alternatives.

The cookie store used for storage is automatically configured to be the best possible option given your application's configuration.

Sessions typically contain at most a user\_id and flash message; both fit within the 4K cookie size limit. A CookieOverflow exception is raised if you attempt to store more than 4K of data.

To configure the session:

```crystal
#  Cookie Store
Amber::Server.instance.session = {
  :key     => "name.session",
  :store   => :cookie,
  :expires => 120, # 0, will make the session last as long as the browser is open, upon closing, session will be terminated
  :secret  => "secret"
}

# Redis Store
Amber::Server.instance.session = {
  :key       => "name.session",
  :store     => :redis,
  :expires   => 120,
  :secret    => "secret",
  :redis_url => "redis://localhost:6379",
}
```

Configure the Pipeline

```crystal
# Keep in mind the order of the Pipes. Session hash needs to be populated before 
# trying to access the session flash scope, the flash depends on the session. 
pipeline :web do
  plug Amber::Pipe::Session.new
  plug Amber::Pipe::Logger.new
  plug Amber::Pipe::Flash.new
  plug Amber::Pipe::CSRF.new
end
```

Accessing the session

```crystal
class ApplicationController < Amber::Controller::Base
  # Finds the User with the ID stored in the session with the key
  # :current_user_id This is a common way to handle user login in
  # a Amber application; logging in sets the session value and
  # logging out removes it.
  private def current_user
    @_current_user ||= session[:current_user_id] &&
      User.find_by(id: session[:current_user_id])
  end
end
```

To store something in the session, just assign it to the key like a hash:

```crystal
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

To remove something from the session, assign that key to be nil or use`session.delete(key)`

```crystal
class LoginsController < ApplicationController
  # "Delete" a login, aka "log the user out"
  def destroy
    # Remove the user id from the session
    @_current_user = session[:current_user_id] = nil
    redirect_to root_url
  end
end
```

## The Flash

The flash is a special part of the session which is cleared with each request. This means that values stored there will only be available on the next request, which is useful for passing error messages etc.

It is accessed in much the same way as the session, as a hash.

Let's use the act of logging out as an example. The controller can send a message which will be displayed to the user on the next request:

```crystal
class LoginsController < ApplicationController
  def destroy
    session[:current_user_id] = nil
    #  Alternatively, `flash.notice=` could be use.
    flash[:notice] = "You have successfully logged out."
    redirect_to root_url
  end
end
```

Rendering the flash message

```html
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

```crystal
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

### Flash.now

By default, adding values to the flash will make them available to the next request, but sometimes you may want to access those values in the same request. For example, if the create action fails to save a resource and you render the new template directly, that's not going to result in a new request, but you may still want to display a message using the flash. To do this, you can use flash.now in the same way you use the normal flash.

```crystal
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

Make sure the Flash, Session and CSRF pipelines are enabled in your `routes.cr` file and in the order that the scaffolding renders them.

# CSRF

To use CSRF, enable the pipe in your `routes.cr`

Then, insert the `csrf_tag` helper in your forms.

## How to use CSRF with Ajax

Simply call the `csrf_tag` helper inside your controller and return it as part of a JSON object:

```crystal
def my_action
    {csrf: csrf_tag}.to_json
end
```

In your Javascript, after getting the JSON object back, refresh your CSRF tag with the one from the server.

```javascript
$("input[name*=_csrf]").replaceWith(e['csrf']);
```



