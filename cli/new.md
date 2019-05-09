# New

## `amber new`

This is the first command that you will run when generating a new Amber application. It creates a new Amber application with a default directory structure and configuration setting at the path you specify.

An `.amber.yml` configuration file is generated a long with the default application directory structure. This stores some metadata for the Amber CLI to make use of for generating migrations, scaffolding templates, and more.

```text
amber new [OPTIONS] NAME

Generates a new Amber project

Arguments:
  NAME  name/path of project

Options:
  -d          Select the database database engine, can be one of: pg | mysql | sqlite
              (default: pg)
  --deps      Installs project dependencies, this is the equivalent of running (shards update)
  -m          Select the model type, can be one of: granite
              (default: granite)
  --no-color  Disable colored output
  -r          Use a named recipe.  See documentation at  https://docs.amberframework.org/amber/cli/recipes.
  -t          Selects the template engine language, can be one of: slang | ecr
              (default: slang)
  -h, --help  show this help
```

See the **Recipes** option of the **Command Line Tool** for information about using recipes to generate applications.

### Example Usage

Using `amber new microsecond-blog` will generate a skeleton Amber application in `./microsecond-blog`. You can have a running web application in a matter of minutes:

```text
amber new microsecond-blog -s sqlite --deps
cd microsecond-blog
amber watch
open http://localhost:3000
```

Full example in terminal:

```text
$ amber new microsecond-blog
Rendering App microsecond-blog in ./microsecond-blog
new       .amber.yml
new       .amber_secret_key
new       .gitignore
new       .travis.yml
new       config/application.cr
new       config/database.yml
... [clipped]
new       src/views/layouts/_nav.slang
new       src/views/layouts/application.slang
new       src/microsecond-blog.cr
$ cd microsecond-blog/
$ shards install
Updating https://github.com/amberframework/amber.git
Updating https://github.com/luislavena/radix.git
... [clipped]
Installing garnet_spec (0.1.1)
$ amber watch        
02:58:23 Watcher    | (INFO) Watching 22 files (server reload)...
02:58:23 Watcher    | (INFO) Building project microsecond-blog...
02:58:31 Watcher    | (INFO) Terminating app microsecond-blog...
02:58:31 Watcher    | (INFO) Starting microsecond-blog...
02:58:31 Server     | (INFO) Amber 0.7.2 serving application "microsecond-blog" at http://0.0.0.0:3000
02:58:31 Server     | (INFO) Server started in development.
02:58:31 Server     | (INFO) Startup Time 00:00:00.000182000
02:58:31 Watcher    | Watching 10 client files...
```

