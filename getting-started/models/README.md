# Models - Granite::ORM

### CRUD: Reading and Writing Data {#crud-reading-and-writing-data}

CRUD is an acronym for the four verbs we use to operate on data:**C**reate,**R**ead,**U**pdate and**D**elete. **Granite::ORM** automatically creates methods to allow an application to read and manipulate data stored within its tables.

```crystal
require "granite_orm/adapter/mysql"

class Post < Granite::ORM
  adapter mysql
  field name : String
  field body : Text
  timestamps
end
```

You can disable the timestamps for SqlLite since TIMESTAMP is not supported for this database:

```crystal
require "granite_orm/adapter/sqlite"

class Comment < Granite::ORM
  adapter sqlite
  table_name post_comments
  field name : String
  field body : Text
end
```

### id, created\_at, updated\_at

The primary key is automatically created for you and if you use`timestamps`they will be automatically updated for you.

Here are the MySQL field definitions for id, created\_at, updated\_at

```sql
id BIGINT NOT NULL AUTO_INCREMENT
# Your fields go here
created_at TIMESTAMP
updated_at TIMESTAMP
PRIMARY KEY (id)
```

### Custom Primary Key

For legacy database mappings, you may already have a table and the primary key is not named`id`or`Int64`.

We have a macro called`primary`to help you out:

```crystal
class Site < Granite::ORM
  adapter mysql
  primary custom_id : Int32
  field name : String
end
```

This will override the default primary key of`id : Int64`.

### SQL

To clear all the rows in the database:

```crystal
Post.clear #truncate the table
```

#### Create

Objects can be created from a hash, a block or have their attributes manually set after creation. The **new **method will return a new object which later can be persisted with **save**

```crystal
post = Post.new
post.name = "Kemalyst Rocks!"
post.body = "Check this out."
post.save
```

#### Read

**Granite::ORM** provides a rich API for accessing data within a database. Below are a few examples of different data access methods provided

#### Find All

```crystal
posts = Post.all
if posts
  posts.each do |post|
    puts post.name
  end
end
```

#### Find

```crystal
post = Post.find 1
if post
  puts post.name
end
```

#### Find By

```crystal
post = Post.find_by :slug, "example_slug"
if post
  puts post.name
end
```

#### Update

Once an Object has been retrieved, its attributes can be modified and it can be saved to the database.

```crystal
post = Post.find 1
post.name = "Granite Really Rocks!"
post.save
```

#### Delete

Likewise, once retrieved an object can be destroyed which removes it from the database.

```crystal
post = Post.find 1
post.destroy
puts "deleted" unless post
```

### Errors

All database errors are added to the`errors`array used by Kemalyst::Validators with the symbol ':base'

```crystal
post = Post.new
post.save
post.errors[0].to_s.should eq "ERROR: name cannot be null"
```

### Queries

The where clause will give you full control over your query. When using the`all`method, the SQL selected fields will always match the fields specified in the model. Always pass in parameters to avoid SQL Injection. Use a`?`\(or`$1`,`$2`,.. for pg\) in your query as placeholder. Checkout the [Crystal DB Driver](https://github.com/crystal-lang/crystal-db) for documentation of the drivers.

Here are some examples:

```crystal
posts = Post.all("WHERE name LIKE ?", ["Joe%"])
if posts
  posts.each do |post|
    puts post.name
  end
end

# ORDER BY Example
posts = Post.all("ORDER BY created_at DESC")

# JOIN Example
posts = Post.all("JOIN comments c ON c.post_id = post.id
                  WHERE c.name = ?
                  ORDER BY post.created_at DESC",
                  ["Joe"])
```



