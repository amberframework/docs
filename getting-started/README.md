# Getting Started

Amber is designed to make common web-development tasks simple, and easy, maintaining developer engagement, productivity without performance penalties. By providing an all-in-one toolbox to get you started the various parts of Amber work well together or separately.

The goal of this overview is to introduce the general concepts in Amber and give you a quick overview of how those concepts are implemented in Amber.

## Conventions Over Configuration

Amber provides a basic organizational structure that covers class names, filenames, database table names, and other conventions. While the conventions take some time to learn, by following the conventions Amber provides you can avoid needless configuration and make a uniform application structure that makes working with various projects simple.

## Model-View-Controller 

Model–view–controller (MVC) is a software architectural pattern for implementing user interfaces on computers. It divides a given application into three interconnected parts. This is done to separate internal representations of information from the way information is presented to, and accepted from, the user. The MVC design pattern decouples these major components allowing for efficient code reuse and parallel development.

### Advantages
- Simultaneous development – Multiple developers can work simultaneously on the model, controller, and views.
- High cohesion – MVC enables logical grouping of related actions on a controller together. The views for a specific model are also grouped together.
- Low coupling – The very nature of the MVC framework is such that there is low coupling among models, views or controllers
- Ease of modification – Because of the separation of responsibilities, future development or modification is easier
Multiple views for a model – Models can have multiple views



### Controller Layer

The Controller is responsible for controlling the application logic and acts as the coordinator between the View and the Model. The Controller receives an input from the users via the View, then processes the user's data with the help of Model and passes the results back to the View.

```crystal
class UsersController < ApplicationController
  def index
    @users = Users.all
    render("index.slang")
  end
end
```

### View Layer

View is the component involved with the application's User Interface. These Views are generally bind from the model data and have extensions such as html, slang, ecr. 

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



