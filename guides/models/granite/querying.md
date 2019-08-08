# Querying

{% hint style="info" %}
This section is based on [Granite's README](https://docs.amberframework.org/granite)
{% endhint %}

## Queries

The where clause will give you full control over your query.

### All

When using the `all` method, the SQL selected fields will always match the fields specified in the model.

Always pass in parameters to avoid SQL Injection. Use a `?` in your query as placeholder. Checkout the [Crystal DB Driver](https://github.com/crystal-lang/crystal-db) for documentation of the drivers.

Here are some examples:

```ruby
posts = Post.all("WHERE name LIKE ?", [%{Joe%}])
if posts
  posts.each do |post|
    puts post.name
  end
end

# ORDER BY Example
posts = Post.all("ORDER BY created_at DESC")

# JOIN Example
posts = Post.all("JOIN comments c ON c.post_id = posts.id
                  WHERE c.name = ?
                  ORDER BY posts.created_at DESC",
                  ["Joe"])
```

### First

It is common to only want the first result and append a `LIMIT 1` to the query. This is what the `first` method does.

For example:

```ruby
post = Post.first("ORDER BY posts.name DESC")
```

This is the same as:

```ruby
post = Post.all("ORDER BY posts.name DESC LIMIT 1").first
```

### Clearing

To clear all the rows in the database:

```ruby
Post.clear #truncate the table
```

### Find All

```ruby
posts = Post.all
if posts
  posts.each do |post|
    puts post.name
  end
end
```

### Find First

```ruby
post = Post.first
if post
  puts post.name
end
```

### Find

```ruby
post = Post.find 1
if post
  puts post.name
end
```

### Find By

```ruby
post = Post.find_by slug: "example_slug"
if post
  puts post.name
end
```

### Find in Batches

```text
find_in_batches(clause = "", params = [] of DB::Any, batch_size limit = 100, offset = 0)
```

```ruby
posts = Post.find_in_batches("name == ?", ["test"])
```

### Find Each

```text
find_each(clause = "", params = [] of DB::Any, batch_size limit = 100, offset = 0)
```

```ruby
posts = Post.find_each("name like ?", ["%test%"]) do |post|
  puts post
end
```

### Insert

```ruby
post = Post.new
post.name = "Granite ORM Rocks!"
post.body = "Check this out."
post.save
```

### Update

```ruby
post = Post.find 1
post.name = "Granite Really Rocks!"
post.save
```

### Delete

```ruby
post = Post.find 1
post.destroy
puts "deleted" unless post
```

