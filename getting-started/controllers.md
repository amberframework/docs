# Controllers {#controllers}

### What Does a Controller Do? {#what-does-a-controller-do-questionmark}

A controller it the middleman between models and views. It makes the model data available to the view so it can display that data to the user, and it saves or updates user data to the model.

Action Controller is the C in MVC. After the router has determined which controller to use for a request, the controller is responsible for making sense of the request and producing the appropriate output. Luckily, **Amber::Controller** Controller does most of the groundwork for you and uses smart conventions to make this as straightforward as possible.

For most conventional [RESTful](http://en.wikipedia.org/wiki/Representational_state_transfer) applications, the controller will receive the request \(this is invisible to you as the developer\), fetch or save data from a model and use a view to create HTML output. If your controller needs to do things a little differently, that's not a problem, this is just the most common way for a controller to work.

### Controller Naming Convention {#controller-naming-convention}

When defining your routes you specify the controller that the route will match to

```crystal
get "/", ApiController, :index
```

In the snipped above the **ApiController** is the name of the controller** **`./src/controller/api_controller.cr`

### Methods and Actions {#methods-and-actions}

A controller is a Crystal class which inherits from `ApplicationController`and has methods just like any other class. When your application receives a request, the routing will determine which controller and action to run, then Rails creates an instance of that controller and runs the method with the same name as the action.

Controllers are defined as such:

```crystal
class ApiController < ApplicationController
  def index
    @client = Client.new
    render("index.slang")
  end
end
```

As an example, if a user goes to `api/index` in your application to add a new client, Amber will create an instance of **ApiController **and call it's **index** method

### Parameters {#parameters}

You will probably want to access data sent in by the user or other parameters in your controller actions. There are two kinds of parameters possible in a web application. The first are parameters that are sent as part of the URL, called query string parameters. The query string is everything after "?" in the URL. The second type of parameter is usually referred to as POST data. This information usually comes from an HTML form which has been filled in by the user. It's called POST data because it can only be sent as part of an HTTP POST request. Amber does not make any distinction between query string parameters and POST parameters, and both are available in the`params`hash in your controller:

```crystal
class UsersController < ApplicationController
    params do
      required(:name) { |p| p.name? & !p.name.empty? }
      required(:email) { |p| p.email? & p.size.between? 1..10 }
    end

    def create
        unless params.valid?
            response.puts {errors: params.errors}.to_json
            response.status_code 400
        end

        user = User.new params.validate!
        user.save!

        @client = Client.new
        redirect_to :index
    end

    # Define parameters per actions
    def update
        update_params = params do
          required(:name, "Your First Name is missing!") { |p| p.name? & !p.name.empty? }
          required(:email, "Your email address is invalid!") { |p| p.email? & p.size.between? 1..10 }
          required(:last_name) { |p| p.last_name? }
        end

        unless update_params.valid?
            response.puts {errors: params.errors}.to_json
            response.status_code 400
        end

        user = User.new update_params.validate!
        user.save!

        redirect_to :index
    end
end
```

With Amber parameters validation is easy to keep your code organize

```crystal
# You can define modules to group your params validations
module UserParams
    def self.create
        params do
          required(:name, "Your First Name is missing!") { |p| p.name? & !p.name.empty? }
          required(:email, "Your email address is invalid!") { |p| p.email? & p.size.between? 1..10 }
          required(:last_name) { |p| p.last_name? }
        end
    end

    def self.update
        params do
          required(:name, "Your First Name is missing!") { |p| p.name? & !p.name.empty? }
          required(:email, "Your email address is invalid!") { |p| p.email? & p.size.between? 1..10 }
          required(:last_name) { |p| p.last_name? }
        end
    end
end

class UsersController < ApplicationController
    include UserParams

    def create
        unless UserParams.create.valid?
            response.puts {errors: params.errors}.to_json
            response.status_code 400
        end

        user = User.new UserParams.create.validate!
        user.save!

        @client = Client.new
        redirect_to :index
    end

    # Define parameters per actions
    def update

        unless UserParams.update.valid?
            response.puts {errors: params.errors}.to_json
            response.status_code 400
        end

        user = User.new UserParams.update.validate!
        user.save!

        redirect_to :index
    end
end
```



