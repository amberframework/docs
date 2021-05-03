# Overview

Amber is designed to make everyday web-development simple and comfortable by maintaining developer engagement and productivity without performance penalties. By providing an all-in-one toolbox, Amber gives developers the leverage to begin developing complex web applications from day one.

The goal of this overview is to introduce the general concepts in Amber and give you a quick overview of how those concepts are implemented.

## Conventions Over Configuration

Amber provides a opinionated organizational structure that covers class names, file names, database table names, and other conventions. While these conventions can take some time to learn, by following Amber's convention over configuration philosophy you can avoid needless configuration decisions, and ensure a uniform application architecture that makes working across various projects and with multiple developers simple.

## Model-View-Controller

Model–view–controller \(MVC\) is a software architectural pattern for implementing user interfaces on computers. It divides a given application into three interconnected parts. The division is done to separate internal representations of information from the way information is presented to, and accepted from, the user. The MVC design pattern decouples these significant components allowing for efficient code reuse and parallel development.

### Advantages

* **Simultaneous development** – multiple developers can work simultaneously on the model, controller, and views.
* **High cohesion** – MVC enables logical grouping of related actions on a controller together. The views for a specific model are also grouped.
* **Low coupling** – the MVC pattern ensures low coupling between models, views or controllers.
* **Ease of modification** – because of the separation of responsibilities, future development or change is easier.
* **Multiple representations for a model** – models can have various views.

### Controller Layer

The Controller is responsible for directing application logic. It does this by acting as the coordinator between the other two components. The Controller receives an input from the users via the View, then processes the user's data with the help of Model and passes the results back to the View.

```ruby
class UsersController < ApplicationController
  def index
    @users = Users.all
    render("index.slang")
  end
end
```

### View Layer

Views are the component involved with the application's User Interface. Views are generally populated with model data and have extensions such as HTML, SLANG, ECR.

```ruby
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

The Model layer represents the central data component of your application and implements the business logic and rules which govern it. The model is responsible for providing a clear interface for your web application to read from and manipulate user data. Such manipulations include processing changes, validating, creating associations to other models and more.

In the case of a social network, the Model layer would take care of tasks such as saving user data, saving friends’ associations, storing and retrieving user photos, finding suggestions for new friends, and more. The model objects are the main entities of your application, such as “Friend,” “User,” “Comment,” or “Photo.” 

For example:

```ruby
class User
  getter name : String
  getter email : String

  def initialize(@name, @email)
  end
end
```
