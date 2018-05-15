# Deploying Amber to Heroku

If you haven't done so please read the guides to install the [Amber CLI](/getting-started/installation/heroku.md)

## The Amber Heroku Buildpack

> Based on [Crystal Heroku Buildpack](https://github.com/crystal-lang/heroku-buildpack-crystal)

You can create an app in Heroku with Amber buildpack by running the following command:

```bash
$ heroku create myapp --buildpack https://github.com/amberframework/heroku-buildpack-amber.git
```

The default behaviour is to use the [latest crystal release](https://github.com/crystal-lang/crystal/releases/latest).
If you need to use a specific version create a `.crystal-version` file in your
application root directory with the version that should be used (e.g. `0.23.1`).

## Requirements

In order for the buildpack to work properly you should have a `shard.yml` file,as it is how it will detect that your app is a Crystal app. Your application has to listen on a port defined by Heroku.


_NOTE: In Amber versions less than or equal to 0.3.7 add the following code to your `config/application.cr` file:_**

```crystal
Amber::Server.configure do |settings|
  settings.host = "0.0.0.0"
  settings.port = ENV["PORT"].to_i if ENV["PORT"]?
end
```

To be able to decrypt and use production environment you'll need to set `ENV["AMBER_ENCRYPTION_KEY"]` to the value of your local projects `.encryption_key` file.

> IMPORTANT: Never add `.encryption_key` to github. Amber adds it by default to your `.gitignore` file.

All that's left is to create a git repository, add the Heroku remote and push it there.

```
$ git init
$ heroku git:remote -a [app-name]
$ git add -A
$ git commit -m "My first Amber app"
$ git push heroku master
```

### Using the Amber CLI on Heroku

When you deploy your project to heroku the amber build-pack will make available the `amber cli` to you via the `bin/amber` path.

To run an amber command just on heroku do the following:

```
heroku run bin/amber [command]
```

> Read more about `heroku run` command https://devcenter.heroku.com/articles/one-off-dynos

### The Heroku Procfile

To deploy your app to heroku you're going to need a `Procfile`. Create a Procfile at the root of your Amber application and add the following:

```
release: bin/amber db migrate
web: bin/{your-app-name}
```

The Amber buildpack takes care of compiling the project for you but if you wish to run your project locally using the heroku command `heroku local` you must do the following steps.

Create an `.env` at the root of your Amber project and add the following 2 lines to the `.env`

```
AMBER_ENV=development
PORT=3000
```

Then you must compile your project locally with the following command:

`crystal build src/{your-app-name}.cr -o bin/{your-app-name}`.

When the compilation process is complete a `bin/{your-app-name}` directory is added, with this binary of your application with this binary file ready you can proceed to run your application locally with heroku.

```
heroku local
```

And should output something similar to:

```
[OKAY] Loaded ENV .env File as KEY=VALUE Format
18:50:27 web.1   |  I, [2017-12-20 18:50:27 -0500 #93113]  INFO -- : [Amber 0.3.7] serving application "heroku-app" at http://0.0.0.0:3000
18:50:27 web.1   |  I, [2017-12-20 18:50:27 -0500 #93113]  INFO -- : Server started in development.
18:50:27 web.1   |  I, [2017-12-20 18:50:27 -0500 #93113]  INFO -- : Startup Time 00:00:00.0003510
```

You're are now all set and ready to deploy with Heroku.
