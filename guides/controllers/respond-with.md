# Respond With

If we need to render a template to html `render("template.slang")` works nicely. A lot of times we want to respond with json, xml, text or something else. In those cases we use `respond_with`.

![respond\_with Demo](https://raw.githubusercontent.com/amberframework/site-assets/master/videos/responding-to-multiple-response-types.gif)

[See video on Youtube](https://www.youtube.com/watch?v=6KNjWDRUo_c)

## Examples Usages

### Responding with JSON

```ruby
class PetController < ApplicationController
  def index
    pets = Pet.all
    respond_with { json pets.to_json }
  end
end
```

### Responding with XML

```ruby
class PetController < ApplicationController
  def index
    pets = Pet.all
    respond_with do
      xml render("index.xml.slang", layout: false)
    end
  end
end
```

### **Responding with Text**

```ruby
class PetController < ApplicationController
  def index
    pets = Pet.all
    respond_with{ text "hello world" }
    end
  end
end
```

### Responding to All

{% hint style="info" %}
Put it all together and you can respond to whatever is asked for.
{% endhint %}

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

