# Amber New Command

This is the first command that you will run when generating a new Amber application.
It creates a new Amber application with a default directory structure and configuration
setting at the path you specify.

An `.amber.yml` configuration file is generated a long with the default application directory
structure. This stores some metadata for the Amber CLI to make use of for generating migrations,
scaffolding templates, and more.

### `amber new`

```shell
amber new OPTIONS NAME

Arguments:
  NAME  name/path of project

Options:
  -d      Select the database database engine, can be one of: pg | mysql | sqlite (default: pg)
  --deps  Installs project dependencies, this is the equivalent of running (shards update)
  -t      Selects the template engine language, can be one of: slang | ecr (default: slang)
  -r      Use a named recipe.  See documentation at https://amberframework.org.
```

See the **Recipes** option of the **Command Line Tool** for information about using
recipes to generate applications.

## Example Usage
Using `amber new microsecond-blog` will generate a skeleton Amber application in `./microsecond-blog`.
You can have a running web application in a matter of minutes:
1. `amber new microsecond-blog`
1. `cd microsecond-blog`
1. `shards install`
1. `amber run &` or `amber watch &` (also can execute these in foreground of separate terminal)
1. `open http://localhost:3000`

Full example in terminal:
```shell
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
Installing selenium (0.3.0)
$ amber run &
[1] 65034
💎  Crystalizing...
microsecond-blog
$ 💎  Crystalization complete!
I, [2017-11-14 10:05:58 -0800 #65094]  INFO -- : [Amber 0.3.0] serving application "microsecond-blog" at http://0.0.0.0:3000
I, [2017-11-14 10:05:58 -0800 #65094]  INFO -- : Server started in development.
I, [2017-11-14 10:05:58 -0800 #65094]  INFO -- : Startup Time 00:00:00.0013140
$ open http://localhost:3000
200  | GET  "/"  | 294.0µs
Params:
```
