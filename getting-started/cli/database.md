# Database Command

The `database` or `db` command is one of the most common command that you will be operating. This
command allows you to work with the database engine of choice when the Amber application was generated.

Here is a list of the commands available:

- **create** Creates the database for the environment specified in your `config/database.yml`
- **drop**   Drops the database for the environment specified in your `config/database.yml`
- **migrate** or **m** Migrates `up` or `down` the database version
- **rollback** Rolls back your database to a specific version or to previous version
- **redo** Repeats the database version specified or lasted version
- **status** Prints the current database migration status
- **seed** Adds initial data after a database is created
- **version** Prints the database current version.

**`amber migrate` or `amber m`**

```shell
amber database [COMMANDS1 COMMANDS2...]

Arguments:
  COMMANDS (Accepts multiple)  drop create migrate rollback redo status version seed
```

## Example Usages

**Create**

```shell
$ amber db create
Created database blog_development
```
*Note: Starting with v0.3.5 - Amber made two changes regarding databases:*
- Amber will substitute hypens (-) for underscores (\_) in database names (keeps it postgresql friendly)
- The default user for pg databases is `postgres` rather than `root`
  - in config/database.yml: `postgres://postgres:@localhost:5432/microsecond_blog_development`
```shell
$ amber new microsecond-blog
... [clipped]
$ cd microsecond-blog
$ amber db create
Created database microsecond_blog_development
```

**Drop**

```shell
$ amber db drop
Dropped database blog_development
```

**Migrate**

```shell
$ amber db migrate
Migrating db, current version: 0, target: 20170928204246
OK   20170928204246_create_post.sql
```

**Rollback**

```shell
$ amber db rollback
Migrating db, current version: 20170928204246, target: 0
OK   20170928204246_create_post.sql
```

**Redo**

```shell
$ amber db redo
Migrating db, current version: 20170928204246, target: 0
OK   20170928204246_create_post.sql
Migrating db, current version: 0, target: 20170928204246
OK   20170928204246_create_post.sql
```

**Status**

```shell
$ amber db status
Applied At                  Migration
=======================================
2017-09-28 20:44:22 UTC  -- 20170928204246_create_post.sql
```

**Seed**

```shell
$ amber db seed
Seeded database
```

**Version**

```shell
$ amber db version
20170928204246
```
