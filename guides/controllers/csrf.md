# CSRF

To use CSRF, enable the pipe in your `routes.cr` by adding the following pipe to a pipeline.

```
plug Amber::Pipe::CSRF.new
```

Then, insert the `csrf_tag` helper in your forms.

## How to use CSRF with AJAX

Simply call the `csrf_tag` helper inside your controller and return it as part of a JSON object:

```ruby
def my_action
    {csrf: csrf_tag}.to_json
end
```

In your Javascript, after getting the JSON object back, refresh your CSRF tag with the one from the server.

```javascript
$("input[name*=_csrf]").replaceWith(e['csrf']);
```
