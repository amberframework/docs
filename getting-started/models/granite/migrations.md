# Migrations

Granite doesn't have migrations built in. Instead, we leveraged the excellent work done by Juan Edi called [micrate](https://github.com/juanedi/micrate).  We have integrated Micrate into the Amber CLI.

## Migration scripts

Migration scripts are created in the `db/migrations` directory.  It's recommended that the files use a timestamp as a way to keep the order of execution.

A micrate script has two sections, Up and Down.  Here is an example `db/migrations/20171003212124_create_post.sql` file:

```sql
-- +micrate Up
CREATE TABLE posts (
  id BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(255),
  body TEXT,
  pages INT,
  published BOOL,
  created_at TIMESTAMP NULL,
  updated_at TIMESTAMP NULL
);

-- +micrate Down
DROP TABLE IF EXISTS posts;
```

## Generators

Amber CLI provides the ability to generate migration scripts:
```bash
amber generate migration create_posts
```

This will generate an empty micrate script with the timestamp set in the filename `db/migrations/20171003212124_create_post.sql`.

If you generate a model or scaffold, it will create a migration script that will create and drop the database table.

```bash
amber generate scaffold post name:string body:text pages:int published:bool
```

This will generate the migration script shown above.

## Run the migrations

You can run the migrations using either:

```bash
amber db migrate
```

You can rollback a migration using either:

```bash
amber db rollback
```

## Drop and Create a database

The `amber db` commmand also allows you to create and drop the database itself:
```
amber db drop create migrate
```

## Seed the database

Sometimes you need to pre-populate your tables with data.  This is common if you need an beginning administrator account or populate lookup tables that rarely change.

You can do this by creating a `db/seeds.cr` file
```crystal
require "../config/application.cr"

user = User.new
user.email = "admin@example.com"
user.password = "password"
user.save
```

This example `seeds.cr` file creates an admin user.

You can execute the seeds using:
```bash
amber db seed
```
