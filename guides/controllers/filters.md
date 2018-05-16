# Filters

Filters are methods that are run "before", "after" or "around" a controller action.

Filters are inherited, so if you set a filter on`ApplicationController`, it will be run on every controller in your application.

## Before filter

"before" filters may halt the request cycle. A common "before" filter is one which requires that a user is logged in for an action to be run. You can define the filter method this way:

```ruby
# Filters are methods that are run "before", "after" a controller action.
before_action do
  # runs for specified actions
  only [:index, :world, :show] { increment(1) }
  # runs for all actions
  all { increment(1) }
end
```

## After filter

"after" filters are executed after the request cycle. A common "after" filter is one which requires to cleanup user data after an action has been run. You can define the filter method this way:

```ruby
after_action do
  # runs for specified actions
  only [:index, :world] { increment(1) }
end
```

