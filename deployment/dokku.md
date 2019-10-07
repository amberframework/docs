# Dokku

## Requirements

Before getting started, ensure you have a Dokku instance running with a new app.
An example app can be created following these steps:

```text
# on the Dokku host
dokku apps:create amber-app

# Setup postgres db
sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git
dokku postgres:create amber-db

# This sets the DATABASE_URL env var
dokku postgres:link amber-db amber-app
```

## Define Buildpack

Buildpacks tell Dokku how to build and deploy your application. Create a file named `.buildpacks` in the root of your project and with this:

```text
https://github.com/amberframework/heroku-buildpack-amber.git
# If you need js / css compiled
https://github.com/heroku/heroku-buildpack-nodejs.git
```

{% hint style="danger" %}
If you want js / css to be compiled, you need to modify the `build` command in `package.json` to `"build": "npm run release",`. Because the nodejs buildpack expects `npm build` to compile the production assets.
{% endhint %}

{% hint style="danger" %}
Not defining a .buildpack file will cause Dokku to build your project using the
`Dockerfile` as Dokku will auto-detect it, this will not work and cause issues.
{% endhint %}

## Define Crystal Version

It's best practice to lock your crystal version to the one you're using. Without
this the latest version of crystal will be used, potentially causing issues in
deployment.

Create a file named `.crystal-version` in the root of your project with the
wanted crystal version:

```
0.31.1
```

## Create Procfile

To deploy your app to Dokku you're going to need a `Procfile`. Create a `Procfile` at the root of your Amber application and add the following:

```yaml
release: bin/amber db migrate
web: bin/{your-app-name}
```

The Amber buildpack takes care of compiling the project for you.

## Env Vars

### Set Production Vars

Where ever your application runs, it needs to be in production mode.

```bash
ssh dokku@{yourdokku.com} config:set {your-app-name} AMBER_ENV=production
```

### Set Encryption Key

Ensure you first run:

```bash
amber encrypt
```

To be able to decrypt and use production environment you'll need to set `ENV["AMBER_ENCRYPTION_KEY"]` to the value of your local projects `.encryption_key` file.

{% hint style="danger" %}
Never add `.encryption_key` to github. Amber adds it by default to your `.gitignore` file.
{% endhint %}

Copy the key from `.encryption_key` and set it to env, like so:

```bash
ssh dokku@{yourdokku.com} config:set {your-app-name} AMBER_ENCRYPTION_KEY={your_key}
```

### Pick up Env vars

In order for the buildpack to work properly your application has to:

* Listen on the environment's port.
* Connect to any services like redis / database.

Ensure these settings are in your `settings.cr` file.

```ruby
Amber::Server.configure do |settings|
  settings.host = "0.0.0.0"
  settings.port = ENV["PORT"].to_i if ENV["PORT"]?
  settings.redis_url = ENV["REDIS_URL"] if ENV["REDIS_URL"]?
  settings.database_url = ENV["DATABASE_URL"] if ENV["DATABASE_URL"]?
end
```

## Deploying

All that's left is to create a git repository, add the Dokku remote and push it there.

```text
$ git init
$ git remote add dokku dokku@{yourdokku.com}:{your-app-name}
$ git add -A
$ git commit -m "My first Amber app"
$ git push dokku master
```

More information on how to deploy your app can be found on the [Dokku docs](http://dokku.viewdocs.io/dokku/deployment/application-deployment/#deploy-tutorial)
