# Callbacks

{% hint style="info" %}
This section is based on [Granite's README](https://amberframework.gitbook.io/granite)
{% endhint %}

There is support for callbacks on certain events.

Here is an example:

```ruby
require "granite_orm/adapter/pg"

class Post < Granite::ORM
  adapter pg

  before_save :upcase_title

  field title : String
  field content : String
  timestamps

  def upcase_title
    if title = @title
      @title = title.upcase
    end
  end
end
```

You can register callbacks for the following events:

## Create

* `before_save`
* `before_create`
* `save`
* `after_create`
* `after_save`

## Update

* `before_save`
* `before_update`
* `save`
* `after_update`
* `after_save`

## Destroy

* `before_destroy`
* `destroy`
* `after_destroy`

