# Halt! Requests


Sometimes we don't want a request to continue executing; maybe an error has occurred or perhaps an operation is taking too long and we would like to abort the execution of the request. Amber provides a `halt!` method to controllers.

When you want a request to cease and return a particular message rather then rendering a page, you use Amber `halt!` to stop the request from continue execution.

```
class UserController < ApplicationController
  def index
    halt(404, "Forbiden") if params[:user_id].nil?
    reder "index.slang"
  end
end
```

A status code of `404` was returned, and the content in `render` will not be delivered to the client.

The next time you’re building a Amber application, consider using halt to simplify error handling.

### Halt! and redirect_to

Unlike other frameworks Amber `redirect_to` stops the current execution of the request and performs the redirection at that given time. 

For example, in other frameworks you will have to do something similar to:

```
class UserController < ApplicationController
  def index
    if some_condition
    	if some_condition
    		redirect_to(path_one) and return
    		# Or another approach
    		return redirect_to(path_one)
		end
    end
  end
end
```

As you probably noticed there is an explicit return, this explicit return is something that is not needed with Amber. Since `redirect_to` uses the `halt!` method in the background.
