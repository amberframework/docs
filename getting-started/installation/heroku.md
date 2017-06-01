# Deploying Amber on Heroku

[Crystal](http://crystal-lang.org/) is a typed, LLVM compiled language that reads \(mostly\) as Ruby. It's a modern language that comes bundled with support for WebSockets, OAuth and other niceties.

### Installing the Amber CLI

The **Amber CLI** offers you a set of command line tools to **create**, **generate**, **scaffold** and **run** Amber instances.

```bash
git clone git@github.com:amber-crystal/amber_cmd.git
cd amber_cmd/
shards install
make
```

You should now be able to run`amber`in the command line.

MacOS optionally, you can use **homebrew** to install.

```
brew update
brew install amber-cmd
```

Now that you successfully have install amber-cmd, you can query to see a list of available commands

```
$ amber --help
amber [OPTIONS] SUBCOMMAND

Amber Cmd

Subcommands:
  c         alias for console
  console

  g         alias for generate
  generate

  n         alias for new
  new

  m         alias for migrate
  migrate

  w         alias for watch
  watch

Options:
  -h, --help     show this help
  -v, --version  show version
```

## Usage

```
amber new [your_app] -d [pg 
|
 mysql 
|
 sqlite] -t [slang 
|
 ecr] --deps 

cd
 [your_app]
```

options:`-d`defaults to pg.`-t`defaults to slang.`--deps`will run`crystal deps`for you.

This will generate a traditional web application:

* **/config** - Application and HTTP::Handler config's goes here. The database.yml and routes.cr are here.
* **/lib** - shards are installed here.
* **/public** - Default location for html/css/js files. The static handler points to this directory.
* **/spec** - all the crystal specs go here.
* **/src** - all the source code goes here, controllers, views, models, presenter, etc.

Generate scaffolding for a resource:

```
amber generate scaffold Post name:string body:text draft:bool
```

This will generate scaffolding for a Post:

* src/controllers/post\_controller.cr
* src/models/post.cr
* src/views/post/\*
* db/migrations/\[datetimestamp\]\_create\_post.sql
* spec/controllers/post\_controller\_spec.cr
* spec/models/post\_spec.cr
* appends route to config/routes.cr
* appends navigation to src/layouts/\_nav.slang

### Run Locally

To test the demo app locally:

1. Create a new Postgres or Mysql database called
   `[your_app]_development`
2. Configure your database with one of the following ways.

   1. Add it in  
      `config/database.yml`

   2. Run \(overrides the`config/database.yml)`  
      `export DATABASE_URL=postgres://[username]:[password]@localhost:5432/[your_app]_development  
      Should output something like:`  
      `Migrating db, current version: 0, target: [datetimestamp] OK [datetimestamp]_create_shop.sql`

1. Run the specs:  
   `crystal spec`

2. Start your app  
   `amber watch`

3. Then visit
   `http://0.0.0.0:3000/`

Note: The`amber watch`command uses[Sentry](https://github.com/samueleaton/sentry)to watch for any changes in your source files, recompiling automatically.

If you don't want to use Sentry, you can compile and run manually:

1. Build the app
   `crystal build --release src/[your_app].cr`
2. Run with
   `./[your_app]`
3. Visit
   `http://0.0.0.0:3000/`



