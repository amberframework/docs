### Queries

The where clause will give you full control over your query.

#### All

When using the `all` method, the SQL selected fields will always match the
fields specified in the model.

Always pass in parameters to avoid SQL Injection.  Use a `?`
in your query as placeholder. Checkout the [Crystal DB Driver](https://github.com/crystal-lang/crystal-db)
for documentation of the drivers.

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

#### First

It is common to only want the first result and append a `LIMIT 1` to the query.
This is what the `first` method does.

For example:

```crystal
post = Post.first("ORDER BY posts.name DESC")
```

This is the same as:

```crystal
post = Post.all("ORDER BY posts.name DESC LIMIT 1").first
```

### Clearing

To clear all the rows in the database:

```crystal
Post.clear #truncate the table
```

#### Find All

```crystal
posts = Post.all
if posts
  posts.each do |post|
    puts post.name
  end
end
```

#### Find First

```crystal
post = Post.first
if post
  puts post.name
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

#### Find in Batches

find_in_batches(clause = "", params = [] of DB::Any, batch_size limit = 100, offset = 0)

```crystal
posts = Post.find_in_batches("name == ?", ["test"])
```

#### Find Each

find_each(clause = "", params = [] of DB::Any, batch_size limit = 100, offset = 0)

```crystal
posts = Post.find_each("name like ?", ["%test%"]) do |post|
  puts post
end
```

#### Insert

```crystal
post = Post.new
post.name = "Granite ORM Rocks!"
post.body = "Check this out."
post.save
```

#### Update

```crystal
post = Post.find 1
post.name = "Granite Really Rocks!"
post.save
```

#### Delete

```crystal
post = Post.find 1
post.destroy
puts "deleted" unless post
```
