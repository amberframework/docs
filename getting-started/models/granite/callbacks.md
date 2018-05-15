# Callbacks

There is support for callbacks on certain events.

Here is an example:

```text
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

* before\_save
* before\_create
* **save**
* after\_create
* after\_save

## Update

* before\_save
* before\_update
* **save**
* after\_update
* after\_save

## Destroy

* before\_destroy
* **destroy**
* after\_destroy

