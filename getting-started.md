# Amber At A Glance

Amber is designed to make common web-development tasks simple, and easy, maintaining developer engagement, productivity without performance penalties. By providing an all-in-one toolbox to get you started the various parts of Amber work well together or separately.

The goal of this overview is to introduce the general concepts in Amber, and give you a quick overview of how those concepts are implemented in Amber.

## Conventions Over Configuration

Amber provides a basic organizational structure that covers class names, filenames, database table names, and other conventions. While the conventions take some time to learn, by following the conventions Amber provides you can avoid needless configuration and make a uniform application structure that makes working with various projects simple.

### Controller Layer

```crystal
class UsersController < ApplicationController
  def index
    @users = Users.all
    render("index.slang")
  end
end
```

### View Layer

```crystal
# Slang - /views/users/index.slang

doctype html
html
  head
    meta name="viewport" content="width=device-width,initial-scale=1.0"
    title This is a title
    css:
      h1 {color: red;} 
      p {color: green;}
    style h2 {color: blue;}
  body
    ul 
     - @users.each do |user|
       li = "#{user.name} - #{user.email}"
```

### Model Layer

The Model layer represents the part of your application that implements the business logic. It is responsible for retrieving data and converting it into the primary meaningful concepts in your application. This includes processing, validating, associating or other tasks related to handling data.

In the case of a social network, the Model layer would take care of tasks such as saving the user data, saving friends’ associations, storing and retrieving user photos, finding suggestions for new friends, etc. The model objects can be thought of as “Friend”, “User”, “Comment”, or “Photo”. If we wanted to load some data from our`users`table we could do:

```ruby
class User
    getter name : String
    getter email : String

    def initialize(@name, @email); end
end
```



