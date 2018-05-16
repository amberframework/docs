# Redirection

Often, we need to redirect to a new url in the middle of a request. A successful `create`action, for instance, will usually redirect to the `show` action for the model we just created. Alternately, it could redirect to the `index` action to show all the things of that same type. There are plenty of other cases where redirection is useful as well.

Calling **redirect\_to** will halt the request lifecycle.

## Redirect to URL

```ruby
redirect_to(location: "", status: 302, params: { "key" => "value" }, flash: { "user_id" => "1" })
```

## Redirect to Action

```ruby
redirect_to(action: :index, status: 302, params: { "key" => "value" }, flash: { "user_id" => "1" })
```

## Redirect to Controller Action

```ruby
redirect_to(
  controller: :symbol, 
  action: :index, 
  status: 302, 
  params: { "key" => "value" }, 
  flash: { "user_id" => "1" })
```

## Redirect Back

```ruby
redirect_back(status: 302, params: { "key" => "value" }, flash: { "user_id" => "1" })
```

