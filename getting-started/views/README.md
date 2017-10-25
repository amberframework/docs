# Views

Views are rendered using the amber `render` macro, which uses [Kilt](http://github.com/jeromegn/kilt). Currently amber
there are 4 different templating languages supported by Kilt: `slang` `ecr`, `mustache`,
and `temel`. Behind the scenes [Kilt](http://github.com/jeromegn/kilt) selects the templating engine based on the
extension of the file so `index.ecr` will render the file using the ECR
engine. Amber currently supports `slang` and `ecr`. Kilt will work with `mustache`
and `temel` as well, although they haven't been tested in amber.  

The Amber Generators support `slang` and `ecr` templates using the `-t` option when you create a new project.

## Slang

To learn slang please refer to [https://github.com/jeromegn/slang](https://github.com/jeromegn/slang).
If you need more examples, Ruby's [slim-lang](http://slim-lang.com) is almost identical to slang.

## ECR \(Embedded Crystal\)

For a primer on ECR please see [https://crystal-lang.org/api/latest/ECR.html](https://crystal-lang.org/api/latest/ECR.html)

# Render method

```cr
render show.slang
# renders src/views/[controller_name]/show.slang
# with default layout "src/views/layouts/application.slang" as defined in ApplicationController or defined in current controller

LAYOUT = "application.slang"

render show.ecr
# same as above except ecr

render("show.slang", layout: "new_layout.slang")
# defines layout on render and ignores global and controller layout.

render("show.slang", layout: false) # Prefered syntax.
render("show.slang", false)
# renders show.slang without layout

render(partial: "_form.slang")
# renders partial without layout. This is really useful for rendering a template inside of your current template.
```

## Examples

The render method is configured to look in the "src/views" path to keep the
controllers simple.  You may also render with a layout which will look for
this in the "src/views/layouts" directory.

```crystal
class HomeController < ApplicationController
  LAYOUT = "application.slang"

  def index
    render "index.slang"
  end
end
```

This will render the `views/home/index.slang` template inside the `views/layouts/application.slang` layout. 

An example `views/home/index.slang`:
```slim
div
  h2 Welcome to Amber Framework!
  p Thank you for trying out the Amber Framework.  We are working hard to provide a super fast and reliable framework that provides all the productivity tools you are used too but not sacrificing the speed.
```

And the layout `views/layouts/application.slang`:
```slim
doctype html
html
  head
    title Blog using Amber
    meta charset="utf-8"
    link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css"

  body
    div.masthead
      div.container
        nav.nav
          == render(partial: "layouts/_nav.slang")
    div.container
      div.row
        div.col-sm-12.main
          == content

    script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.3/jquery.min.js"
    script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/js/bootstrap.min.js"
    script src="/dist/main.bundle.js"
```

The `content` variable holds the rendered view that will be injected in the layout.

## Passing data from Controllers to Views

All local variables assigned in the controller action are available in the views.  This is because the views are compiled in the same scope as the controller action.  There is no copying or passing data between the view and controller like other frameworks.  This keeps memory consumption lower and improves performance significantly.
