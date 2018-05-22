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

## Something else doesn't work

Ask around on the [Amber Gitter channel](https://gitter.im/amberframework/amber), or [create an issue](https://github.com/amberframework/amber/issues).

If you figure it out, edit this document as a courtesy to the next person having the same problem.

