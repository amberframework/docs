# Params

## Introduction

When building a web application at some point you will want to access data sent from a client. Amber makes the data received from an HTTP Request available to the controller via the params object. 

The params object parses the data from the request, this includes:

1. Query String parameters: the query string is everything after "?" in the URL. 
2. Form input parameters: these are send as HTML form posts
3. Route URL parameters: route url parameters are define within the resource url.

## Array Parameters

The params object is not limited to one-dimensional keys and values. It can contain nested arrays and hashes. To send an array of values, append an empty pair of square brackets "[]" to the key name

If you need get array of params from query:

```text
?brand[]=brand1&brand[]=brand2&brand[]=brand3
```

To retrieve all possible values for a key:

```ruby
params.fetch_all("brand[]") # => { "brand[]" => ["brand1", "brand2", "brand3"] }
```
> The value of params["brand[]"] will now be ["brand1", "brand2", "brand3"]. Note that parameter values are always strings; Amber makes no attempt to guess or cast the type.

## JSON Parameters

When writing a Web Service application that accepts JSON data, the application most likely will need to parse the incomming JSON payload. When the "Content-Type" header of your request is set to "application/json", Amber will automatically load your parameters into the params object, which you can access as you would normally.

## Routing Parameters

Any other parameters defined by the routing, such as :id, will also be available in the `params` object. As an example, consider a listing of clients where the list can show either active or inactive clients. We can add a route which captures the `:status` parameter in a "pretty" URL:

```crystal
get '/clients/:status' => ClientsController, :index
```

In this case, when a user opens the URL `/clients/active`, `params[:status]` will be set to "active"

## Validating request parameters

Performing validations at the params level can save your application from performing operations deemed to be invalid due to input params. Having parameter validations in place prevents errors due to invalid input and increases the security of your application in general.

Validating params and erroring early in the request lifecycle frees resources for the next request sooner, adds a layer of security and prevents invalid data from reaching the backend processes.

Amber attempts to aliviate the issues that come with invalid parameters and provides a `params` object to all controllers which contains a built-in `validation` method.

### Benefits

- Expression and explicitness about the parameters the model exptects.
- Security by whitelisting only the parameters allowed per action.
- Data correctnes to prevent invalid inputs to propagate in the system.

### Example Usage

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

### Validation API

`#validation` Setup validation rules to be performed.

- Use `#required(field)` to define required fields.
- Use `#optional(field)` to define optional fields.

```ruby
params.validation do
  required(:email) { |p| p.url? }
  optional(:age)
end
```

`#validate!` Input must be valid otherwise raises an error. If valid, returns a hash of validated params otherwise raises `Validator::ValidationFailed` which conatins the failed validaton error messages.

```ruby
user = User.new params.validate!
```

`#valid?` Returns true if all params are valid or false otherwise. 

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

### Field Validation Rules

Amber has extrended the Crystal String and Number classes with additional methods to assit with better validation.

| String                      | Number          |
|-----------------------------|-----------------|
| `str?`                        | `positive?`       |
| `email?`                      | `negative?`       |
| `domain?`                     | `zero?`           |
| `url?`                        | `div?(n)`         |
| `ipv4?`                       | `above?(n)`       |
| `ipv6?`                       | `below?(n)`       |
| `mac_address?`                | `lt?(num)`        |
| `hex_color?`                  | `self?(num)`      |
| `hex?`                        | `lteq?(num)`      |
| `alpha?(locale = "en-US")`    | `between?(range)` |
| `numeric?`                    | `gteq?(num)`      |
| `alphanum?(locale = "en-US")` |                 |
| `md5?`                        |                 |
| `base64?`                    |                 |
| `slug?`                       |                 |
| `lower?`                      |                 |
| `upper?`                      |                 |
| `credit_card?`                |                 |
| `phone?(locale = "en-US")`    |                 |
| `excludes?(value)`            |                 |
| `time_string?`                |                 |`

### Organizing validations

With Amber parameter validation it's easy to keep your code organized:

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

