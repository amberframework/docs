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
```

## Example Usage

```shell
± amber new blog
Rendering App blog in ./blog
new       .amber.yml
new       .amber_secret_key
new       .gitignore
new       .travis.yml
new       config/application.cr
new       config/database.yml
new       config/deploy/Dockerfile
new       config/environments/development.yml
new       config/environments/production.yml
new       config/environments/test.yml
new       config/routes.cr
new       config/webpack/common.js
new       config/webpack/development.js
new       config/webpack/production.js
new       docker-compose.yml
new       Dockerfile
new       package.json
new       public/crossdomain.xml
new       public/dist/images/logo.png
new       public/dist/main.bundle.js
new       public/robots.txt
new       README.md
new       shard.yml
new       spec/spec_helper.cr
new       src/assets/fonts/.gitkeep
new       src/assets/images/.gitkeep
new       src/assets/images/logo.png
new       src/assets/javascripts/main.js
new       src/assets/stylesheets/main.scss
new       src/controllers/application_controller.cr
new       src/controllers/home_controller.cr
new       src/controllers/static_controller.cr
new       src/handlers/.gitkeep
new       src/mailers/.gitkeep
new       src/models/.gitkeep
new       src/views/home/index.slang
new       src/views/layouts/_nav.slang
new       src/views/layouts/application.slang
new       src/blog.cr
```

This generates a skeletal Amber installation in ~/Code/blog.
