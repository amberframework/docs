# Amber Generator Recipes

Amber generators can use a recipe to generate your application, and scaffold your
application with controllers, models and views.  Using a recipe you can get started
with an amber application that extends or modifies the standard built in generator
or provides additional features.  For example you might want an API application
that only renders JSON or use React or AngularJS for views.

## Recipe repository

Visit the [recipe repository](https://github.com/AmberRecipes/recipes) to discover
what recipes are available. The available recipes are listed in the Contributions.md
file and you can drill down into each sub folder for more details about each recipe.

# Amber New Command with a recipe

This is the first command that you will run when generating a new Amber application.
It creates a new Amber application with a default directory structure and configuration
setting at the path you specify.

An `.amber.yml` configuration file is generated along with the default application directory
structure and any additional items or modifications provided by the recipe.
This stores some metadata for the Amber CLI to make use of for generating migrations,
scaffolding templates, and more.  The recipe name that you use to generate the application is saved
in the `.amber.yml` file so that when you generate controllers, models and views with the
scaffold generator the same recipe is used - you don't have to specify the recipe each time.

### `amber new`

```shell
amber new NAME OPTIONS

Arguments:
  NAME  name/path of project

Options:
  -d      Select the database database engine, can be one of: pg | mysql | sqlite (default: pg)
  --deps  Installs project dependencies, this is the equivalent of running (shards update)
  -t      Selects the template engine language, can be one of: slang | ecr (default: slang)
  -r      Use a named recipe.  See documentation at https://amberframework.org.
```

## Example Usage
Using `amber new microsecond-blog -r damianham/modular` will generate a skeleton
Amber application in `./microsecond-blog` using the _modular_ recipe that was
provided by the github user _damianham_.

You can have a running web application in a matter of minutes:
1. `amber new microsecond-blog -r damianham/modular`
1. `cd microsecond-blog`
1. `shards install`
1. `amber run &` or `amber watch &` (also can execute these in foreground of separate terminal)
1. `open http://localhost:3000`

Full example in terminal:
```shell
$ amber new microsecond-blog -r damianham/modular
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

# Amber Generate Command

The generators in amber are a great way to get an application up and running quickly.
In addition, they help keep code consistent and following convention.  See the
**Generate** option of the **Command Line Tool** for details about the generate command.

Once you have generated an application with a recipe, the same recipe will be used
by the generate command when you generate controllers, models or scaffold controllers,
models and views with the scaffold generator.

# Custom Recipe

One of the recipes available from the recipe repository may almost suit your requirements,
but not quite.  The recipes are just a set of files that are templates for various
parts of an application.  You can download and extract a recipe and modify the templates
to better suit your requirements (and then contribute your modified recipe to the repository so
everyone can benefit from your changes).

Download the recipe and extract it to a folder and then give the path to the folder as
the recipe command line argument.  For example;

1. `wget https://raw.githubusercontent.com/AmberRecipes/recipes/master/damianham/modular.zip`
1. `mkdir ~/mymodular`
1. `unzip -d ~/mymodular modular.zip`
1. `# modify the recipe in ~/mymodular some way`
1. `amber new microsecond-blog -r ~/mymodular`
1. `cd microsecond-blog`
1. `shards install`
1. `amber run &` or `amber watch &` (also can execute these in foreground of separate terminal)
1. `open http://localhost:3000`

It is important to give the full path to the recipe folder as the recipe command line
argument to ensure that the recipe can be found when generating subsequent artifacts.

# Custom Repository

You can specify the location of a recipe repository in the `.amber.yml` file so that you can use
recipes from an independent source.  Add the **recipe_source** item to the '.amber.yml' file
with the URL of the website or website folder that contains the recipe zip files to use the recipes
from the given URL. E.g

```
type: app
database: sqlite
language: slang
model: granite
recipe: myrecipe
recipe_source: http://my.example.org/recipes
```

The scaffold generator would then use a recipe located at
`http://my.example.org/recipes/myrecipe.zip`


## Clear Recipe Cache

If you set a recipe source URL you should also change the name of the recipe and/or
you should clear the recipe cache otherwise a recipe with the same name in the
recipe cache will be used rather than a recipe from the given recipe source.

The recipe cache is located in the current working directory and is created any time
you specify a recipe name that is downloaded from the repository.  Since you would
usually specify a recipe when creating a new application the recipe cache will be
located in the parent folder of the new application in a folder called `.amber_recipe_cache`.
