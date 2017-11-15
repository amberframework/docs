# Getting Started

Amber is designed to make common web-development tasks simple, and easy, maintaining developer engagement, productivity without performance penalties. By providing an all-in-one toolbox to get you started the various parts of Amber work well together or separately.

The goal of this overview is to introduce the general concepts in Amber and give you a quick overview of how those concepts are implemented in Amber.

## Guide Assumptions

This guide is designed for beginners who want to get started with a Amber application from scratch. It does not assume that you have any prior experience with Amber. However, to get the most out of it, you need to have some prerequisites installed:

* The [Crystal ](https://crystal-lang.org/)language version 0.23.1 or higher
* The [Shards](https://github.com/crystal-lang/shards) packaging system, which is installed with Crystal by default. 
* A working installation of the [SQLite3 Database](https://www.sqlite.org/)

Amber is a web application framework running on the Crystal programming language. If you have no prior experience with Crystal, you will find a very steep learning curve diving straight into Amber. There are several curated lists of online resources for learning Crystal:

* [Official Learn Crystal Docs](https://crystal-lang.org/docs/overview/)

Be aware that the Crystal language is under development

## What is Amber?

Amber is a web application development framework written in the Crystal language. It is designed to make programming web applications easier by making assumptions about what every developer needs to get started. It allows you to write less code while accomplishing more than many other languages and frameworks. Experienced Amber developers also report that it makes web application development more fun.

Amber is opinionated software. It makes the assumption that there is a "best" way to do things, and it's designed to encourage that way - and in some cases to discourage alternatives.

The Amber philosophy includes two major guiding principles:

* **Don't Repeat Yourself:**
  DRY is a principle of software development which states that "Every piece of knowledge must have a single, unambiguous, authoritative representation within a system." By not writing the same information over and over again, our code is more maintainable, more extensible, and less buggy.
* **Convention Over Configuration:**
  Amber provides a basic organizational structure that covers class names, filenames, database table names, and other conventions. While the conventions take some time to learn, by following the conventions Amber provides you can avoid needless configuration and make a uniform application structure that makes working with various projects simple.

## Creating a New Amber Project

Open up a command line prompt. On macOS open Terminal.app, on Windows choose "Run" from your Start menu and type `cmd.exe`. Any commands prefaced with a dollar sign `$` should be run in the command line. Verify that you have a current version of Ruby installed:

```bash
$ crystal -v
Crystal 0.23.1 (2017-09-08) LLVM 4.0.1
```

Many popular UNIX-like OSes ship with an acceptable version of SQLite3. On Windows, if you installed Rails through Rails Installer, you already have SQLite installed. Others can find installation instructions at the [SQLite3 website](https://www.sqlite.org/). Verify that it is correctly installed and in your PATH

```bash
$ sqlite3 --version
```

The program should report its version.

To install Amber, use

## Model-View-Controller

Model–view–controller \(MVC\) is a software architectural pattern for implementing user interfaces on computers. It divides a given application into three interconnected parts. This is done to separate internal representations of information from the way information is presented to, and accepted from, the user. The MVC design pattern decouples these major components allowing for efficient code reuse and parallel development.

### Advantages

* Simultaneous development – Multiple developers can work simultaneously on the model, controller, and views.
* High cohesion – MVC enables logical grouping of related actions on a controller together. The views for a specific model are also grouped together.
* Low coupling – The very nature of the MVC framework is such that there is low coupling among models, views or controllers
* Ease of modification – Because of the separation of responsibilities, future development or modification is easier
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



