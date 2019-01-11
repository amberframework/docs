# Sessions

## The Session

A session store that uses an Amber:Router::Session::Store to store the sessions. This store is most useful if you don't store critical data in your sessions and you don't need them to live for extended periods of time.

This cookie-based session store is the Amber default. It is dramatically faster than the alternatives.

The cookie store used for storage is automatically configured to be the best possible option given your application's configuration.

Sessions typically contain at most a user\_id and flash message; both fit within the 4K cookie size limit. A CookieOverflow exception is raised if you attempt to store more than 4K of data.

To configure the session:

```ruby
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

Accessing the session

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

To remove something from the session, use`session.delete(key)`

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

## CSRF

To use CSRF, enable the pipe in your `routes.cr`

Then, insert the `csrf_tag` helper in your forms.

### How to use CSRF with Ajax

Simply call the `csrf_tag` helper inside your controller and return it as part of a JSON object:

```ruby
def my_action
    {csrf: csrf_tag}.to_json
end
```

In your Javascript, after getting the JSON object back, refresh your CSRF tag with the one from the server.

```javascript
$("input[name*=_csrf]").replaceWith(e['csrf']);
```

