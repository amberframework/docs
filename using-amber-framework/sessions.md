# Sessions

Session object works just as in Rails.

```crystal
session[:name] = “Merlin”
puts session[:name] # this will work on any page once it has been set as expected.
```

# Flash

Flash object works like in Rails. If you're unfamiliar, it is cleared after it is read.

```crystal
flash[:error] = "Not enough brains."
puts flash[:error] # will display the message and clear on reload
```



