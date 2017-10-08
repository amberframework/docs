# Amber Controllers

### What Does a Controller Do?

A controller is the middleman between models and views. It makes the model data available to the view so it can display that data to the user, and it saves or updates user data to the model.

The controller is the C in MVC. After the router has determined which controller to use for a request, the controller is responsible for making sense of the request and producing the appropriate output. Luckily, **Amber::Controller::Base** does most of the groundwork for you and uses smart conventions to make this as straightforward as possible.

For most conventional [RESTful](http://en.wikipedia.org/wiki/Representational_state_transfer) applications, the controller will receive the request \(this is invisible to you as the developer\), fetch or save data from a model and use a view to create HTML output. If your controller needs to do things a little differently, that's not a problem, this is just the most common way for a controller to work.

### Controller Naming Convention

When defining your routes you specify the controller that the route will match to

```crystal
get "/", ApiController, :index
```

In the snipped above the **ApiController** is the name of the controller `./src/controller/api_controller.cr`

### Methods and Actions

A controller is a Crystal class which inherits from `ApplicationController`and has methods just like any other class. When your application receives a request, the routing will determine which controller and action to run, then Amber creates an instance of that controller and runs the method with the same name as the action.

Controllers are defined as such:

```crystal
class ApiController < ApplicationController
  def index
    @client = Client.new
    render("index.slang")
  end
end
```

As an example, if a user goes to `api/index` in your application to add a new client, Amber will create an instance of **ApiController** and call it's **index** method

### Actions Conventions

While actions can be named anything we like, there are conventions for action names which you should adhere whenever possible. We went over these in the Routing Guide, but we’ll take another quick look here.

- index - renders a list of all items of the given resource type
- show - renders an individual item by id
- new - renders a form for creating a new item
- create - receives parameters for one new item and saves it in a persistent store
- edit - retrieves an individual item by id and displays it in a form for editing
- update - receives parameters for one edited item and saves it to a persistent store
- delete - receives an id for an item to be deleted and deletes it from a persistent store

