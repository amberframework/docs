# Deploying Amber to Heroku

If you haven't done so please read the guides to install the [Amber CLI](/getting-started/installation/heroku.md)

## Heroku

If you have generated your project with the Amber CLI and successfully run it locally we are now at a point where we can create an application on [Heroku](https://www.heroku.com/). If you haven't yet done it, create an account on Heroku and install the [Heroku Toolbelt](https://toolbelt.heroku.com/).

The Crystal Language is not supported out of the box by Heroku, so we'll need to use a custom buildpack.

You can create an app in Heroku with Crystal's buildpack by running the following command:

```
$ heroku create myapp --buildpack https://github.com/crystal-lang/heroku-buildpack-crystal.git
```

The default behaviour is to use the [latest crystal release](https://github.com/crystal-lang/crystal/releases/latest). If you need to use a specific version create a `.crystal-version`file in your application root directory with the version that should be used \(e.g.`0.17.1`\).

### Requirements

In order for the buildpack to work properly you should have a`shard.yml`file, as it is how it will detect that your app is a Crystal app.

Your application has to listen on a port defined by Heroku. It is given to you through the command line option`--port`and the environment variable`PORT`\(accessible through`ENV["PORT"]`in Crystal\). However, Amber should handle this for you.

All that's left is to create a git repository, add the Heroku remote and push it there. Don't forget to add`.deps, .crystal`and `libs`to`.gitignore.`

```
$ git init
$ heroku git:remote -a [app-name]
$ git add -A
$ git commit -m "My first Amber app"
$ git push heroku master
```



