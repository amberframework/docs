# Params Validation

You will probably want to access data sent in by the user or other parameters in your controller actions. There are two kinds of parameters possible in a web application. The first are parameters that are sent as part of the URL, called query string parameters. The query string is everything after "?" in the URL. The second type of parameter is usually referred to as POST data. This information usually comes from an HTML form which has been filled in by the user. It's called POST data because it can only be sent as part of an HTTP POST request. Amber does not make any distinction between query string parameters and POST parameters, and both are available in the`params`hash in your controller:

```crystal
class UsersController < ApplicationController
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

    # Define parameters within actions
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
    def create_paramms
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



