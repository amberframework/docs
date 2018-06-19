# From Scratch

Also you can create an Amber project from scratch using `crystal init app`.

```text
$ crystal init app myapp
      create  myapp/.gitignore
      create  myapp/.editorconfig
      create  myapp/LICENSE
      create  myapp/README.md
      create  myapp/.travis.yml
      create  myapp/shard.yml
      create  myapp/src/myapp.cr
      create  myapp/src/myapp/version.cr
      create  myapp/spec/spec_helper.cr
      create  myapp/spec/myapp_spec.cr
Initialized empty Git repository in /home/main/myapp/.git/
```

## Add Amber dependency

First you require to add the `amber` shard dependency in your `shard.yml` file:

```yaml
name: myapp
version: 0.1.0

authors:
  - Foo Bar <foo@bar.baz>

targets:
  myapp:
    main: src/myapp.cr

crystal: 0.24.2

license: MIT

dependencies:
  amber:
    github: amberframework/amber
    version: 0.7.2
```

Then execute `shards install` :

```text
$ shards install
Fetching https://github.com/amberframework/amber.git
Fetching https://github.com/amberframework/router.git
Fetching https://github.com/amberframework/cli.git
Fetching https://github.com/mosop/optarg.git
Fetching https://github.com/mosop/callback.git
Fetching https://github.com/mosop/string_inflection.git
Fetching https://github.com/elorest/compiled_license.git
Fetching https://github.com/jeromegn/kilt.git
Fetching https://github.com/amberframework/micrate.git
Fetching https://github.com/crystal-lang/crystal-db.git
Fetching https://github.com/crystal-lang/crystal-mysql.git
Fetching https://github.com/will/crystal-pg.git
Fetching https://github.com/stefanwille/crystal-redis.git
Fetching https://github.com/jwaldrip/shell-table.cr.git
Fetching https://github.com/jeromegn/slang.git
Fetching https://github.com/crystal-lang/crystal-sqlite3.git
Fetching https://github.com/phoffer/inflector.cr.git
Fetching https://github.com/amberframework/teeplate.git
Installing amber (0.7.2)
Installing amber_router (0.0.3)
Installing cli (0.7.0)
Installing optarg (0.5.8)
Installing callback (0.6.3)
Installing string_inflection (0.2.1)
Installing compiled_license (0.1.3)
Installing kilt (0.4.0)
Installing micrate (0.3.1)
Installing db (0.5.0)
Installing mysql (0.4.0)
Installing pg (0.14.1)
Installing redis (1.9.0)
Installing shell-table (0.9.2)
Installing slang (1.7.1)
Installing sqlite3 (0.9.0)
Installing inflector (0.1.8)
Installing teeplate (0.5.0)
```

## Create project structure

All Amber's projects have a [directory structure](../guides/directory-structure.md). Basically you need a common file tree \(unless you use a [Minimal Configuration](../examples/minimal-configuration.md)\).

```text
.
├── config
│   ├── application.cr
│   └── routes.cr
└── src
    ├── controllers
    │   └── application_controller.cr
    └── myapp.cr
```

First you need an `config/`folder with an `application.cr` file, also see [configuration](../guides/configuration.md).

{% code-tabs %}
{% code-tabs-item title="config/application.cr" %}
```ruby
require "amber"

require "../src/controllers/**"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Secondly a `routes.cr` file with pipelines and routes blocks, also see: [routing](../guides/routing/).

{% code-tabs %}
{% code-tabs-item title="config/routes.cr" %}
```ruby
Amber::Server.configure do |app|
  pipeline :web do
    # plug PipeName.new
  end

  routes :web do
    # get "/", SomeController, :some_action
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Then a basic setup \(without views nor models\) requires an `application_controller.cr` file inside `src/controllers` directory.

{% code-tabs %}
{% code-tabs-item title="src/controllers/application\_controller.cr" %}
```ruby
class ApplicationController < Amber::Controller::Base
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Finally you need to call `Amber::Server` and all your project in your main `myapp.cr`file.

{% code-tabs %}
{% code-tabs-item title="src/myapp.cr" %}
```ruby
require "../config/*"

Amber::Server.start
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Build and run your project

To compile your project use `shards build myapp`and run the executable with `bin/myapp`.

```text
$ shards build myapp
Dependencies are satisfied
Building: myapp
$ bin/myapp 
06:52:37 Server     | (INFO) Amber 0.7.2 serving application "Amber_app" at http://localhost:3000
06:52:37 Server     | (INFO) Server started in development.
06:52:37 Server     | (INFO) Startup Time 00:00:00.000273000
```

That's it!, now you're ready to create any basic project from scratch without Amber CLI.

