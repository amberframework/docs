# Minimal Configuration

Amber can be run from a single file for minimal configuration setups if the user prefers this for smaller applications.

```crystal
require "amber"

class HelloController < Amber::Controller::Base
  def index
    "hello world"
  end
end

Amber::Server.configure do |app|
  pipeline :api do
  end

  routes :api do
    get "/", HelloController, :index
  end
end

Amber::Server.start
```
