---
description: Respond to multiple request types easily and conveniently
---

# Respond With

If we need to render a template to html `render("template.slang")` works nicely. A lot of times we want to respond with json, xml, text or something else. In those cases, we can use `respond_with`.

Amber will use 2 methods to determine which response type to use:

1. The `accepts` header
2. A url extension

These are the currently supported response types:

```crystal
html: "text/html"
json: "application/json"
txt:  "text/plain"
text: "text/plain"
xml:  "application/xml"
js:   "application/javascript"
```

## Usage

```ruby
class PetController < ApplicationController
  def index
    pets = Pet.all
    respond_with do
      html render("index.slang")
      
      # Each response type also accepts a block
      # The JSON response would be triggered from the following
      # GET /pets/index.json
      # GET /pets/index with the `accepts: application/json` header
      json do 
        pets.to_json
      end
      
      # This is another valid block format
      xml { render("index.xml.slang", layout: false) }
      text { "Here are your pets #{pets.try(&.join(", ")}" }
      txt "Here are your pets #{pets.try(&.join(", ")}"
    end
  end
end
```

If you do not specify a response type with the `accepts` header or using the dot notation in the url, you will get the first defined response type. In this example you would get the `html` response.
