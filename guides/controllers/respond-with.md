# Respond With

If we need to render a template to html `render("template.slang")` works nicely. A lot of times we want to respond with json, xml, text or something else. In those cases we can use `respond_with`.

## Basic Usage

```ruby
class PetController < ApplicationController
  def index
    pets = Pet.all
    respond_with do
      html render("index.slang")
      json pets.to_json
      xml render("index.xml.slang", layout: false)
      text "hello world"
    end
  end
end
```

## **Block Syntax**

```ruby
class PetController < ApplicationController
  def index
    pets = Pet.all
    respond_with do
      html do
        flash[:notice] = "Success"
        render("index.slang")
      end
      
      text do
        string = "hello"
        string += " world
        string
      end
  end
end
```

## Demo
[See video on Youtube](https://www.youtube.com/watch?v=6KNjWDRUo_c)
![respond\_with Demo](https://raw.githubusercontent.com/amberframework/site-assets/master/videos/responding-to-multiple-response-types.gif)
