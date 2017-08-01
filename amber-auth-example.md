# Amber Auth Example

This is an example of how to handle authentication with Amber.

Source Code: https://github.com/drujensen/amber-auth-example 

## Setup

Let's create a new application:

```
amber new auth -d mysql --deps
cd auth
```

Next, we scaffold a new User model:

```
amber generate scaffold User email:string encrypted_password:string
```

and we will need a sessions controller to handle login and logout:

```
amber generate controller Session new create delete
```

Create the database using mysql console:

```
create database auth_development;
```

Run migrations:

```
amber migrate up
```

If this fails, check your`config/database.yml`and make sure it points to your mysql db.

## Code

Now for some code. In the`models/user.cr`, we need to create the`encrypted_password`and verify that the passwords match. We will use the standard library`crypto/bcrypt/password`

```crystal
require "kemalyst-model/adapter/mysql"
require "crypto/bcrypt/password"

class User < Granite::Model
  adapter mysql

  # id : Int64 primary key is created for you
  field email : String
  field encrypted_password : String
  timestamps

  def password=(password)
    @encrypted_password = Crypto::Bcrypt::Password.create(password, cost: 10).to_s
  end

  def authenticate(password : String)
    if enc = @encrypted_password
      bcrypt_password = Crypto::Bcrypt::Password.new(enc)
      return bcrypt_password == password
    else
      return false
    end
  end
end
```

We added two methods. A setter for`password=`and an`authenticate`method.

The setter will encrypt the password and store it in the`encrypted_password`field in the database.

The`authenticate`method will check to make sure the password provided matches the`encrypted_password`.

Next, we need to update the controller and the view to ask for the`password`instead of the`encrypted_password`.

In the`controllers/user_controller.cr`

```crystal
def create
  user = User.new(params.to_h.select(["email"))
  user.password = params["password"]
  ...
end

...

def update
  if user = User.find(params["id"])
    user.set_attributes(params.to_h.select(["email"]))
    user.password = params["password"]
    ...
end
```

We remove setting the`encrypted_password`directly and use the new`password=`setter instead.

In the`views/users/_form.slang`:

```slang
...
form action="#{ action }" method="post"
  == csrf_tag
  - if user.id
    input type="hidden" name="_method" value="patch"
  div.form-group
    input.form-control type="text" name="email" placeholder="Email" value="#{ user.email }"
  div.form-group
    input.form-control type="password" name="password" placeholder="password"
  button.btn.btn-primary.btn-xs type="submit" Submit
  a.btn.btn-default.btn-xs href="/users" back
```

We add the`== csrf_tag`and replace the`encrypted_password`input with the`password`input. We also change the type of input to`password`.

Ok. Now lets move on to the`SessionController`and create the login screen.

In the`controllers/session_controller.cr`:

```crystal
class SessionController < ApplicationController 
  def new
    render("new.slang")
  end

  def create
    email = params["email"]
    password = params["password"]
    user = User.find_by :email,  email
    if user && user.authenticate(password)
        session["user_id"] = user.id.to_s
        flash["info"] = "Successfully logged in"
        redirect_to "/"
      else
        flash["danger"] = "Invalid email or password"
        render("new.slang")
      end
  end

  def delete
    context.clear_session
    flash["info"] = "Logged out.  See ya later!"
    redirect_to "/"
  end
end
```

Now we need a login page. Remove the`views/session/create.slang`and`views/session/delete.slang`and update the`views/session/new.slang`:

```
form action="/session" method="post"
  == csrf_tag
  div.form-group
    input.form-control type="email" name="email" placeholder="Email"
  div.form-group
    input.form-control type="password" name="password" placeholder="Password"
  button.btn.btn-primary.btn-xs type="submit" Login
```

Let's update the`config/routes.cr`and replace the`session`paths with`/login`and`/logout`paths:

```crystal
  ...
  routes :web do
    get "/login", SessionController, :new
    post "/session", SessionController, :create
    get "/logout", SessionController, :delete
    resources "/users", UserController
    get "/", HomeController, :index
  end
  ...
```

Ok, we are almost there.

The next part is the most complicated. We are going to create some middleware that will perform the authentication.

Create a new file in`src/middleware/authenticate.cr`:

```crystal
class HTTP::Server::Context
  property current_user : User?
end

class Authenticate < Amber::Pipe::Base
  def call(context)
    user_id = context.session["user_id"]?
    if user = User.find(user_id.to_s)
      context.current_user = user
      call_next(context)
    else
      return call_next(context) if ["/login","/session"].includes?(context.request.path)
      context.flash["warning"] = "Please login"
      context.response.headers.add "Location", "/login"
      context.response.status_code = 302
    end
  end
end
```

This change may need some explanation.

The first thing we are doing is re-opening the context and adding a`current_user`property. This allows us to access the`context.current_user`on any page including the layout.

Next we are creating a new Amber::Pipe that will perform the authentication. This is similar to what Devise does in a rails app.

If we find the user, we set the`current_user`in the context, otherwise we redirect to the`/login`page.

One exception is if we are on the`/login`or`/session`path, we want to skip redirecting to the login page again.

Now we need to add this Pipe to the`:web`pipeline in the \`config/routes.cr:

```crystal
require "../src/middleware/*"

Amber::Server.instance.config do |app|
  pipeline :web do
    # Plug is the method to use connect a pipe (middleware)
    # A plug accepts an instance of HTTP::Handler
    plug Amber::Pipe::Logger.new
    plug Amber::Pipe::Session.new
    plug Amber::Pipe::Flash.new
    plug Amber::Pipe::CSRF.new
    plug Authenticate.new
  end
...
```

We enable the`CSRF`pipe and add the`Authenticate`pipe.

Don't forget to require the`..src/middleware/*`.

Finally, let's update the`views/layouts/_nav.slang`so it hides the`users`navigation and shows a`login`,`logout`:

```
- active = context.request.path == "/" ? "active" : ""
a class="nav-item #{active}" href="/" Home
- if context.current_user
  a class="nav-item pull-right" href="/logout" Logout
  - active = context.request.path == "/users" ? "active" : ""
  a class="nav-item #{active}" href="/users" Users
- else
  - active = context.request.path == "/login" ? "active" : ""
  a class="nav-item #{active} pull-right" href="/login" Login
```

## Seeds

Create a new file`db/seeds.cr`:

```crystal
require "amber"
require "../src/models/*"

user = User.new
user.email = "admin@example.com"
user.password = "password"
user.save
```

Seed the database by running:

```
crystal db/seeds.cr
```

## Run

```
amber w
```



