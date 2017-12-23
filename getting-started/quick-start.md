# Getting Started with Amber
This getting started guide will help you get a full stack web application running in just a few minutes by leveraging amber’s code generators.

After we have a working application, then we will walk through the code that is making it all happen and code some enhancements along the way.

Let’s get started!

## Install dependencies
If you already have `crystal`, `amber`, and a database installed, you can skip this step.

### Install `crystal`

#### OS X with homebrew
_You can skip this step if installing amber with homebrew, it installs crystal_
 ```
brew install crystal-lang
```
#### Ubuntu or Debian
 ```
curl https://dist.crystal-lang.org/apt/setup.sh | sudo bash
sudo apt-get install build-essential crystal
```

#### Full Install Instructions
See full installation instructions [here](https://crystal-lang.org/docs/installation/)

### Install `amber`

#### OS X with homebrew
_This will also install crystal_
```
brew install amberframework/amber/amber
```
_Note: Soon, you should be able to use `brew install amber`_

#### Ubuntu or Debian (Installing from Source)
_Below are the steps for building from source, the dependencies are specific to Ubuntu/Debian. See [full installation instructions](https://amberframework.org/guides/getting-started/Installation/README.md#installation) for other Linux Distributions_
```
sudo apt-get install build-essential libreadline-dev libsqlite3-dev libpq-dev libmysqlclient-dev libssl-dev libyaml-dev
curl -L https://github.com/amberframework/amber/archive/stable.tar.gz | tar xz
cd amber-stable/
shards install
make install
```

#### Full Install Instructions
See full installation instructions [here](https://amberframework.org/guides/getting-started/Installation/README.md#installation) for RedHat & CentOS, ArchLinux & Derivatives, and more complete instructions.

### Install a database
Amber works with `postgresql` (default), `mysql`, or `sqlite`.

If you don’t already have one of these installed, please follow the guides provided by each database maintainer:
[Postgresql Installation Guides](https://wiki.postgresql.org/wiki/Detailed_installation_guides)
On OS X postgres can also be installed with homebrew or the Postgres.app
[MySQL Installation Guides](https://dev.mysql.com/doc/refman/8.0/en/installing.html)
On OS X mysql can also be installed with homebrew
[Tutorial on installing SQLite](https://www.tutorialspoint.com/sqlite/sqlite_installation.htm)
On OS X sqlite3 can also be installed with homebrew

## Generate a new Amber application
With all dependencies successfully installed, we can generate a new application with `amber new`

After the code for the new application is generated, we will `cd` into the new directory and execute a `shards install`.

The shards install command may take a little while - it has to download all shard dependencies.
```
# The default setup will use a postgresql database, use `-d mysql` or `-d sqlite` for mysql and sqlite, respectively
amber new pet-tracker
cd pet-tracker
shards install
```

## Generate a resource
With the skeleton application generated, we can generate our first RESTful resource.

 The `amber generate scaffold` command will help us do this.

```
# 'g' is shorthand for 'generate'
amber g scaffold Pet name:string breed:string age:integer
```

## Create and migrate the database
Generating the application and the scaffolded resource provides the configuration and migration files needed to setup our database.

`amber db` will help us do this.
```
amber db create migrate
```
This will create a new database and run the migration to create a `pets` table with the specified columns.

## Build the application and run the server
We can use `amber watch` to both build the binary application and start the server. Additionally, `amber watch` will detect code changes then recompile and restart the application automatically.
```
amber watch
```

## Use your brand new web application!
Open any browser and goto http://localhost:3000
You should see a home page load and “Pets” in the nav bar.
If you click on the “Pets” link, you should be able to perform all 7 RESTful actions for the “pets” resource.

## Deploy your web application

[Digital Ocean Deployment Guide](/guides/recipes/examples/digital-ocean-deployments.md#digital-ocean-deployments)
[Heroku Deployment Guide](/guides/recipes/examples/heroku-deployments.md#heroku-deployments)

## List of Commands
```
amber new pet-tracker
cd pet-tracker
shards install
amber generate scaffold Pet name breed age:integer weight:integer
amber db create migrate
amber watch
```
