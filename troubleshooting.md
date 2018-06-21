# Troubleshooting

This is a place to share common problems and solutions to them.

## Can't build my amber project

Sometimes, you are trying to build an amber project in a new machine and crystal doesn't have all dependencies required by default. Then, ensure to install all development packages for `openssl` `git`, `yaml`, `libevent`, and `sqlite3`, `postgresql`, or `mysql`.

{% hint style="info" %}
Also see [Installation Guide](guides/installation.md)
{% endhint %}

## Can't connect to database

Sometimes, you're trying to run your amber project and you get an database connection error. Then, ensure your database is running and the `database_url` is well specified on `config/environments/{your-enviroment-file}.yml`, or on `DATABASE_URL` environment variable. Also remember to run `bin/amber db drop create migrate` , before executing your project.

{% hint style="info" %}
Also see [Creating the Database](guides/create-new-app.md#creating-the-database)
{% endhint %}

## Can't update dependencies

On new [crystal 0.25.0 release](https://crystal-lang.org/2018/06/15/crystal-0.25.0-released.html), the `shards` command has a global cache issue, already reported [here](https://github.com/crystal-lang/shards/issues/211) and fixed on next version. Until crystal 0.25.1 is released, you can fix your dependencies by removing global shards cache and shards files.

```text
rm -rf ~/.cache/shards
rm -rf shard.lock
rm -rf lib
```

## Something else doesn't work

Ask around on the [Amber Gitter channel](https://gitter.im/amberframework/amber), or [create an issue](https://github.com/amberframework/amber/issues).

If you figure it out, edit this document as a courtesy to the next person having the same problem.

