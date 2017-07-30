# Using Jennifer ORM with Amber - Installation

### What is Jennifer?

**Jennifer** is  an ActiveRecord pattern realization for Crystal with grate query DSL and migration mechanism.

#### Example App Repo
https://github.com/eliasjpr/amber-jennnifer-app-example

## Installing Jennifer for Amber

#### Generate a normal amber app

```bash
amber new {project}
```

#### Update your project shard.yml

Add this to your application's

```yaml
# Add the following dependencies
jennifer:
  github: imdrasil/jennifer.cr

sam:
  github: imdrasil/sam.cr
```

> Also** **you need to choose one of existing adapters for your db: [mysql](https://github.com/crystal-lang/crystal-mysql) or [postgres](https://github.com/will/crystal-pg).

Then in the console

```bash
shards update
```

#### Setup your database information

```yaml
defaults : &defaults
  host: localhost
  adapter: postgres
  user: root
  password: somepassword
  migration_files_path: db/migrations # this is the default location for all migrations

development:
  db: blog_development
  <<: *defaults

test:
  db: blog_test
  <<: *defaults
```

#### Create a **jennifer.c**r under the **/config** directory

```crystal
require "colorize"

Jennifer::Config.read("config/database.yml", AMBER_ENV)

Jennifer::Config.configure do |conf|
  conf.logger = Logger.new(STDOUT)

  conf.logger.formatter = Logger::Formatter.new do |severity, datetime, progname, message, io|
    io << datetime.colorize(:cyan) << ": \n" << message.colorize(:light_magenta)
  end
  conf.logger.level = Logger::DEBUG
end
```

> Note that we pass the AMBER\_ENV to Jennifer::Config.read this will allow Jennifer to use the correct database settings for the environment.

#### Create a sam.cr in {project/src}

Jennifer uses Sam for running tasks pertinent to ORM operations. Sam is a Make-like utility which allows to specify tasks like Ruby's Rake do using plain Crystal. For how to use [Sam](https://github.com/imdrasil/sam.cr) visit the Github repository [https://github.com/imdrasil/sam.cr](https://github.com/imdrasil/sam.cr)

```crystal
# src/sam.cr
require "jennifer/adapter/postgres"
require "jennifer"

require "../config/jennifer"
require "../db/migrations/*"
require "sam"
require "jennifer/sam"
load_dependencies "jennifer"
Sam.help
```

#### Edit your src/{project}.cr file

This should be done before you load your application configurations \(or at least models\). With Amber this is very easy. Also the order is very important the adapter should come before Jennifer.

```crystal
require "jennifer/adapter/postgres" # for postgres
require "jennifer"

require "amber"
require "./controllers/**"
require "./mailers/**"
require "./models/**"
require "./views/**"
require "../config/*"

Amber::Server.instance.run
```

You're all set with the configuration. Next using Jennifer Migrations and Models.

