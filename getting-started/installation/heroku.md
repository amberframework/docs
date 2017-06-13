# Installing Amber CLI

The **Amber CLI** offers you a set of command line tools to **create**, **generate**, **scaffold** and **run** Amber.

### Installing the Amber CLI

```bash
git clone git@github.com:amber-crystal/amber_cmd.git
cd amber_cmd/
shards install
make
```

You should now be able to run`amber`in the command line.

MacOS optionally, you can use **homebrew** to install.

```
brew tap amber-crystal/amber
brew install amber-crystal/amber/amber
```

Now that you successfully have install amber-cmd, you can query to see a list of available commands

    $ amber --help
    amber [OPTIONS] SUBCOMMAND

    Amber::CMD

    The `amber new` command creates a new Amber application with a default
    directory structure and configuration at the path you specify.

    You can specify extra command-line arguments to be used every time
    `amber new` runs in the .amber.yml configuration file in your project 
    root directory

    Note that the arguments specified in the .amber.yml file does not affect the
    defaults values shown above in this help message.

    Usage:
    amber new [app_name] -d [pg | mysql | sqlite] -t [slang | ecr] --deps 

    Commands:
      amber c console                 # Starts a amber console   
      amber g generate [SUBCOMMAND]   # Generate Amber classes
      amber n new                     # Generate a new amber project
      amber m migrate [SUBCOMMAND]    # Performs database migrations tasks
      amber w watch                   # Starts amber server and rebuilds on file changes
      amber routes                    # Prints the routes (In Development)
      amber r run [OPTION]            # Compiles and runs your project. Options: [-p --port | -e -environment]

    Options:
      -t, --template [name]           # Preconfigure for selected template engine. Options: slang | ecr 
      -d, --database [name]           # Preconfigure for selected database. Options: pg | mysql | sqlite
      -h, --help                      # Describe available commands and usages
      -v, --version                   # Prints Amber version
      --deps                          # Installs project dependencies

    Example:
      amber new ~/Code/Projects/weblog
      This generates a skeletal Amber installation in ~/Code/Projects/weblog.

```
amber new [your_app] -d [pg | mysql | sqlite] -t [slang | ecr] --deps 
cd [your_app]
```

options:`-d`defaults to pg.`-t`defaults to slang.`--deps`will run`crystal deps`for you.

### Directory Structure

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

3. Run the specs:  
   `crystal spec`

4. Start your app  
   `amber watch`

5. Then visit  
   `http://0.0.0.0:3000/`

> Note: The`amber watch`command uses [Sentry](https://github.com/samueleaton/sentry) to watch for any changes in your source files, recompiling automatically.

If you don't want to use Sentry, you can compile and run manually:

1. Build the app
   `crystal build --release src/[your_app].cr`
2. Run with
   `./[your_app]`
3. Visit
   `http://0.0.0.0:3000/`



