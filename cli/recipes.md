# Recipes

## Recipes

Amber generators can use a recipe to generate your application, and scaffold your application with controllers, models and views. Using a recipe you can get started with an amber application that extends or modifies the standard built in generator or provides additional features. For example you might want an API application that only renders JSON or use React or AngularJS for views.

### Recipe contributors

Recipes are provided by independent contributors in their own github accounts.  To use a recipe you specify the github account name and recipe name in the format _githubaccount/recipe_name_.  

### Available recipes

At this time the known available recipes are;

- [damianham/amber_granite](https://github.com/damianham/amber_granite) - basic default recipe using Granite ORM
- [damianham/amber_crecto](https://github.com/damianham/amber_crecto) - basic default recipe using Crecto ORM
- [damianham/amber_modular](https://github.com/damianham/amber_modular) - modular structure for your application using Granite ORM

Visit the recipe repository for more information about each recipe.

### Amber New Command with a recipe

Add a recipe name command line argument to create a new amber application from the recipe.  
The recipe name that you use to generate the application is saved so that when you generate controllers, models and views with the scaffold generator the same recipe is used - you don't have to specify the recipe each time.

#### `amber new`

```text
amber new [OPTIONS] NAME

Generates a new Amber project

Arguments:
  NAME  name/path of project

Options:
  -d          Select the database database engine, can be one of: pg | mysql | sqlite
              (default: pg)
  --deps      Installs project dependencies, this is the equivalent of running (shards update)
  -m          Select the model type, can be one of: granite | crecto
              (default: granite)
  --no-color  Disable colored output
  -r          Use a named recipe.  See documentation at  https://docs.amberframework.org/amber/cli/recipes.
  -t          Selects the template engine language, can be one of: slang | ecr
              (default: slang)
  -h, --help  show this help
```

### Example Usage

Using `amber new microsecond-blog -r damianham/amber_modular` will generate a skeleton Amber application in `./microsecond-blog` using the _amber_modular_ recipe that was provided by the github user _damianham_.

You can have a running web application in a matter of minutes:

1. `amber new microsecond-blog -r damianham/amber_modular --deps`
2. `cd microsecond-blog`
3. `amber watch`

Now open a web browser for your new amber app at [http://localhost:3000](http://localhost:3000).

Full example in terminal:

```text
$ amber new microsecond-blog -r damianham/amber_modular
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
$ amber watch
02:58:23 Watcher    | (INFO) Watching 22 files (server reload)...
02:58:23 Watcher    | (INFO) Building project App01...
02:58:31 Watcher    | (INFO) Terminating app App01...
02:58:31 Watcher    | (INFO) Starting App01...
02:58:31 NodeJS     | (INFO) Installing dependencies...
02:58:31 NodeJS     | (INFO) Watching public directory
02:58:31 Server     | (INFO) Amber 0.10.0 serving application "App01" at http://0.0.0.0:3000
02:58:31 Server     | (INFO) Server started in development.
02:58:31 Server     | (INFO) Startup Time 00:00:00.000182000
02:58:31 Watcher    | Watching 10 client files...
```

### Amber Generate Command

The generators in amber are a great way to get an application up and running quickly. In addition, they help keep code consistent and following convention. See the **Generate** option of the **Command Line Tool** for details about the generate command.

Once an application is generated with a recipe, it will be used by future generate or scaffold commands to maintain consistency with the recipe. For example, an application generated with a "json-api" recipe will continue to generate controllers and views according to the "json-api" recipe.

### Custom Recipe

When a recipe doesn't quite suite your requirements, it's easy to modify. Recipes are application stubs written in [Liquid](https://github.com/TechMagister/liquid.cr).  
Modified recipes can be stored and sourced locally or made available to other Amber users from your own github account.

Download a recipe that you want to modify, extract it, and use the extracted recipe on the command line. For example:

1. `git clone https://github.com/damianham/amber_modular.git ~/mymodular`
2. `# modify the recipe in ~/mymodular some way`
3. `amber new microsecond-blog -r ~/mymodular`
4. `cd microsecond-blog`
5. `shards install`
6. `amber watch`

Now open a web browser at [http://localhost:3000](http://localhost:3000).

It is important to give the absolute path to the recipe folder as the recipe command line argument to ensure that the recipe can be found when generating subsequent artifacts.

### Contributing your recipe

The best way to do this is to fork an existing recipe and then modify it.  Visit the github repository of a recipe you want to use as a starting point, e.g. one of the recipes listed above in the **Available recipes** section, and create your own fork.  After you have modified the recipe and pushed your changes you can create apps with your recipe with:

```
amber new myapp -r myour_github_name/your_recipe_name
```

You can easily contribute your recipe to the Amber community. Create a fork of [amber-docs](https://github.com/amberframework/docs), edit this page to add your recipe to the **Available recipes** section and submit a Pull Request.
