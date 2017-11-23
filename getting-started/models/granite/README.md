# Granite::ORM

## Installation

Add this library to your projects dependencies along with the driver in
your `shard.yml`.  This can be used with any framework but was originally
designed to work with the amber framework in mind.  This library will work
with kemal or any other framework as well.

```yaml
dependencies:
  granite_orm:
    github: amberframework/granite-orm

  # Pick your database
  mysql:
    github: crystal-lang/crystal-mysql

  sqlite3:
    github: crystal-lang/crystal-sqlite3

  pg:
    github: will/crystal-pg
```

Next you will need to set the `database_url` to point to your database.  

You will set this in the `config/environments/development.yml` and `config/environments/test.yml`

```yaml
database_url: "mysql://username:password@host:3306/database_environment"
# or
database_url: "postgres://username:password@host:5432/database_environment"
# or
database_url: "sqlite3:./config/database_environment.db"
```

Or you can set the `DATABASE_URL` environment variable.  This will override the environments setting.

## Usage

Here is an example using Granite ORM Model

```crystal
require "granite_orm/adapter/mysql"

class Post < Granite::ORM::Base
  adapter mysql
  field name : String
  field body : String
  timestamps
end
```

You can disable the timestamps for SqlLite since TIMESTAMP is not supported for this database:
```crystal
require "granite_orm/adapter/sqlite"

class Comment < Granite::ORM::Base
  adapter sqlite
  table_name post_comments
  field name : String
  field body : String
end
```

### id, created_at, updated_at

The primary key is automatically created for you and if you use `timestamps` they will be
automatically updated for you.

Here are the MySQL field definitions for id, created_at, updated_at

```mysql
id BIGINT NOT NULL AUTO_INCREMENT
# Your fields go here
created_at TIMESTAMP
updated_at TIMESTAMP
PRIMARY KEY (id)
```

### Custom Primary Key

For legacy database mappings, you may already have a table and the primary key is not named `id` or `Int64`.

We have a macro called `primary` to help you out:

```crystal
class Site < Granite::ORM::Base
  adapter mysql
  primary custom_id : Int32
  field name : String
end
```

This will override the default primary key of `id : Int64`.
