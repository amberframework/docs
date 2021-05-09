# Jennifer

## What is Jennifer?

**Jennifer** is an ActiveRecord-like ORM for Crystal with a custom DSL for queries and migrations.

{% hint style="info" %}
This section is based on [Jennifer Docs](https://github.com/imdrasil/jennifer.cr/blob/master/docs/index.md). Also see [Amber Jennifer Example App](https://github.com/eliasjpr/amber-jennnifer-app-example).
{% endhint %}

## Installing Jennifer for Amber
By default, Amber applications ship with 
[Granite](https://docs.amberframework.org/granite) a lightweight ORM for Crystal applications. To begin using Jennifer in your Amber application you will need to follow a few steps to get up and running.

### Generate a normal amber app
Begin by generating a new amber project (if you have not already).

```bash
amber new {project}
cd project
```

### Update your project shard.yml
Once your Amber project is created, you will need to update your `shard.yml` file to include Jennifer as a dependency.

Add the following code snippet to your application's `shard.yml` file:

```yaml
# Jennifer is an ORM
jennifer:
  github: imdrasil/jennifer.cr

# SAM is a CLI utility for running migrations, creating or dropping tables
sam:
  github: imdrasil/sam.cr

```

In addition to the Jennifer dependency, you may have noticed we included an additional shard called `sam`. The `sam` shard is simple command line utility for running common database tasks such as creating migrations, adding, or dropping tables. While technically optional it is recommended. 

Once you've added the required dependencies, you will need to decide what database you would like to use with your Amber application. By default, Jennifer ships with SQLite support, however, should you want to use PostgreSQL or MySQL, you will need to add in a compatible database adapter to your `shard.yml` file. 

```yaml
# snipped shards 

# Your choice of database adapter

# PostgreSQL
pg:
	github: will/crystal-pg
	version: "= 0.21.0"

# MySQL
crystal-mysql:
	github: crystal-lang/crystal-mysql
	version: "= 0.11.0"
```

You can read about the Crystal community adapters below:

* [MySQL](https://github.com/crystal-lang/crystal-mysql) 
* [PostgreSQL](https://github.com/will/crystal-pg).

Once you have updated your dependencies and chosen an appropriate adapter (if any), proceed to update your project's shards by running:

```bash
shards update
```

This command will begin installing Jennifer and its associated dependencies into your Amber project.

### Setup your database information
Once Jennifer is up an running as a dependency in your project, you will need to create a new database configuration. Inside of your project's `config` directory create a new file called `config/database.yml` and fill in the code snippet below:

```yaml
defaults : &defaults
  host: localhost
  adapter: postgres
  user: <add-your-database-user>
  password: <add-your-database-password>
  migration_files_path: db/migrations # this is the default location for all migrations

development:
  db: blog_development
  <<: *defaults

test:
  db: blog_test
  <<: *defaults
```

The `config/database.yml` describes the various databases Jennifer will use in your project's different environments.

### Add A Jennifer Initializer to Configuration
With your `config/database.yml` in place, we need to inform Amber about Jennifer and tell the framework how we would like to configure the ORM. To begin, you will need to create a `config/jennifer.cr` file in your project's `config` directory. Below is a basic setup for Jennifer and you are also encourage to [see an example Amber Jennifer App] for more inspiration.

```ruby
require "amber"
require "colorize"

Jennifer::Config.read("config/database.yml", Amber.env.to_s)

Jennifer::Config.configure do |conf|
  conf.logger = Logger.new(STDOUT)

  conf.logger.formatter = Logger::Formatter.new do |severity, datetime, progname, message, io|
    io << datetime.colorize(:cyan) << ": \n" << message.colorize(:light_magenta)
  end
  conf.logger.level = Logger::DEBUG
end
```

{% hint style="info" %}
Note that we pass the `AMBER_ENV` to `Jennifer::Config.read` this will allow Jennifer to use the correct database settings for the environment.
{% endhint %}

### Create a sam.cr in {project/src}
As mentioned previously, Jennifer uses Sam for running tasks pertinent to ORM operations. Sam is a Make-like utility which allows to specify tasks like Ruby's Rake do using plain Crystal. For how to use [Sam](https://github.com/imdrasil/sam.cr) visit the Github repository [https://github.com/imdrasil/sam.cr](https://github.com/imdrasil/sam.cr)

Create a new `sam.cr` file inside your project's `src` directory. 

```ruby
# src/sam.cr
require "jennifer"
require "jennifer/adapter/postgres"

require "../config/jennifer"
require "../db/migrations/*"
require "sam"
require "jennifer/sam"
load_dependencies "jennifer"
Sam.help
```

This file operationalizes `sam` to begin running tasks. You can run `crystal sam.cr -- help` to get a list of available tasks once the file is added.

### Including Jennifer in Server Bootstrap 
The final step in configuration is to include Jennifer in your `src/{project}.cr` file. This should be done before you load your application configurations (or at least models). 

```ruby
require "jennifer"
require "jennifer/adapter/postgres"

require "amber"
require "./controllers/**"
require "./mailers/**"
require "./models/**"
require "./views/**"
require "../config/*"

Amber::Server.instance.run
```

You're all set with the configuration. Next using Jennifer Migrations and Models.
