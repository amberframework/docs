# Generating a New Amber Application

Before we begin, please take a minute to read the [Installation Guide](getting-started/Installation/README.md). By installing any necessary dependencies beforehand, we’ll be able to get our application up and running smoothly.

At this point, we should have Crystal and Amber installed. We should also have PostgreSQL and NodeJS installed to build a default application.

To verify you have Amber installed, run the command `amber -v` in your terminal window:

```bash
$ amber -v
Amber CMD (amberframework.org) - v0.2.6
```

## Bootstrap an Amber Application

We can run `amber new` from any directory to bootstrap our Amber application. Amber will accept either an absolute or relative path to the directory of our new project. Assuming that the name of our application is "weblog", let’s run the following command:

```bash
$ amber new weblog
```

Additionally, you can pass the following options to the above command:

* `-d` This specifies the database driver to use, it defaults to pg, for PostgreSQL.
* `-t` This specifies the template rendering engine it defaults to slang, the Slim-inspired templating language.
* `--deps` This will download and install project dependencies for you, saving you an additional step.

Amber generates the directory structure and files needed for your application.

```bash
Rendering App weblog in ./weblog
new       shard.yml
new       .amber.yml
new       config/database.yml
new       config/application.cr
new       config/deploy/Dockerfile
new       config/environments/development.yml
new       config/environments/production.yml
new       config/environments/test.yml
...
new       src/assets/fonts/.gitkeep
new       src/assets/stylesheets/main.scss
new       src/weblog.cr
new       src/handlers/.gitkeep
```

After the application is created, change your current working directory to it:

```bash
cd weblog
```

Then install project dependencies with `shards install`:

```bash
Installing amber (0.2.6)
Installing radix (master)
Installing kilt (0.3.3)
Installing slang (master)
...
Installing sqlite3 (0.8.2)
Installing granite_orm (0.7.3)
Installing quartz-mailer (0.1.0)
Installing smtp (master)
Installing jasper_helpers (0.1.5)
Installing amber_spec (0.1.1)
```

Now we’ll create the database:

> Amber assumes that your PostgreSQL database will have a `root` user account with the correct permissions and no password set for this user. If that isn’t the case, update the `config/database.yml` with the correct database credentials for your environment.

With your database credentials ready run the following command in your terminal window:

```bash
amber db create
```

\(This creates your app Postgres database\) and should output

```bash
Created database weblog_development
```

And finally, we’ll start the Amber server:

```bash
amber watch
```

By default Amber accepts requests on port 3000. If we point our favorite web browser at [http://localhost:3000](http://localhost:3000), we should see the Amber Framework welcome page

![](https://github.com/amberframework/online-docs/blob/master/assets/amber-framework-welcome.png?raw=true)  

If your screen looks like the image above, congratulations! You now have a working Amber application. In case you can’t see the page above, try accessing it via [http://127.0.0.1:3000](http://127.0.0.1:3000) and later make sure your OS has defined “localhost” as “127.0.0.1”.

Locally, our application is running in an Crystal process. To stop it, we hit ctrl-c once, just as we would to terminate the program normally.

> **Note:** The `amber watch` command uses [Sentry](https://github.com/samueleaton/sentry) to watch for any changes in your source files, recompiling automatically. If you don't want to use Sentry, you can compile and run manually:  
> 1. Build the app `crystal build --release src/[your_app].cr`  
> 2. Run with `./[your_app]`  
> 3. Visit `http://0.0.0.0:3000/`



