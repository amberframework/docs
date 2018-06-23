# Request Params Validation

## Introduction

You will probably want to access data sent in by the user or other parameters in your controller actions. There are two types of request parameters possible in a web application:

* The first type of parameters sent as part of the URL, called query string parameters. The query string is everything after "?" in the URL.
* The second type of parameter is usually referred to as POST data. This information often comes from an HTML form which has been submitted by a user and, called POST data because it can sent as part of an HTTP POST request.

## Validating request parameters

Performing validations at the params level can save your application to perform operations deemed to be invalid due to input params. Having params validations in place prevents your application for errors due to wrong input and increases the security of your app in general.

With Params Validation erroring early in the request lifecycle is excellent, allows to free resources for the next request, adds a layer of security and prevents invalid data from reaching the backend processes.

Amber attempts to aliviate the issues that comes with invalid parameters, and  provides a `params` object to all controllers in which contains a validation method.

## Benefits

- Expression and explicitnes about the parameters the model exptects.
- Security by whitelisting only the parameters allowed per action.
- Data correctnes to prevent invalid inputs to propagate in the system.

## Example Usage

```ruby
class UsersController < ApplicationController
  
  def create
    user = User.new(user_params.validate!)
    
    if user.save
      redirect_to action: :index, flash: {:success => "Created user successfully!"}
    else
      redirect_to action: :index, flash: {:error => "Could not create user!"}
    end
  end
  
  def user_params
    params.validation do
     required(:name, "Your First Name is missing!") { |p| p.name? & !p.name.empty? }
     required(:email, "Your email address is invalid!") { |p| p.email? & p.size.between? 1..10 }
     required(:last_name) { |p| p.last_name? }
    end
  end
end
```

## Validation API

`#validation` Setups validation rules to be performed.

- Use `#required(field)` method to define require fields.
- Use `#optional(field)` method to define optional fields.

```ruby
params.validation do
  required(:email) { |p| p.url? }
  optional(:age)
end
```

`#validate!` Input must be valid otherwise raises error, if valid returns a hash of validated params Otherwise raises a Validator::ValidationFailed error messages contain errors.

```ruby
user = User.new params.validate!
```

`#valid?` Returns True or false whether the validation passed

```ruby
unless params.valid?
  response.puts {errors: params.errors}.to_json
  response.status_code 400
end
```

`#errors` Returns an array of errors for the invalid inputs. This array of errors is populated after running `#validate!` or `#valid?`.

```ruby
params.errors
```

# Field Validation Rules

Amber has extrended the Crystal String and Number classes with additional methods to assit with better validation.

| String                      | Number          |
|-----------------------------|-----------------|
| str?                        | positive?       |
| email?                      | negative?       |
| domain?                     | zero?           |
| url?                        | div?(n)         |
| ipv4?                       | above?(n)       |
| ipv6?                       | below?(n)       |
| mac_address?                | lt?(num)        |
| hex_color?                  | self?(num)      |
| hex?                        | lteq?(num)      |
| alpha?(locale = "en-US")    | between?(range) |
| numeric?                    | gteq?(num)      |
| alphanum?(locale = "en-US") |                 |
| md5?                        |                 |
| base64?                     |                 |
| slug?                       |                 |
| lower?                      |                 |
| upper?                      |                 |
| credit_card?                |                 |
| phone?(locale = "en-US")    |                 |
| excludes?(value)            |                 |
| time_string?                |                 |

## Organizing validations

With Amber parameter validation, it's easy to keep your code organized:

```ruby
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

