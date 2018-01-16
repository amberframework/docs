# Working with Views

Views are rendered using the amber `render` macro, which uses [Kilt](http://github.com/jeromegn/kilt). Currently amber
there are 4 different templating languages supported by Kilt: `slang` `ecr`, `mustache`,
and `temel`. Behind the scenes [Kilt](http://github.com/jeromegn/kilt) selects the templating engine based on the
extension of the file so `index.ecr` will render the file using the ECR
engine. Amber currently supports `slang` and `ecr`. Kilt will work with `mustache`
and `temel` as well, although they haven't been tested in amber.  

The Amber Generators support `slang` and `ecr` templates using the `-t` option when you create a new project.

- **Slang** To learn slang please refer to [https://github.com/jeromegn/slang](https://github.com/jeromegn/slang).
If you need more examples, Ruby's [slim-lang](http://slim-lang.com) is almost identical to slang.

- **ECR \(Embedded Crystal\)** For a primer on ECR please see [https://crystal-lang.org/api/latest/ECR.html](https://crystal-lang.org/api/latest/ECR.html)

# Rendering Content

In most cases, the `Controller::Base#render` method does the heavy lifting of rendering your application's content for use by a browser. There are a variety of ways to customize the behavior of render. You can render the default view for a Amber template, or a specific template, or a file, or inline code, or nothing at all.

### Rendering an Action's View

If you want to render the view that corresponds to a different template within the same controller, you can use render with the name of the view:

```
def update
  @post = Post.find(params[:id])
  if @post.update(post_params)
    redirect_to(@post)
  else
    render "edit.slang"
  end
end
```

### Rendering an Action's Template from Another Controller

If you want to render a template from an entirely different controller from the one that contains the action code, You can accomplish that with `render`, which accepts the full path (relative to `src/views`) of the template to render. 

For example, if you're running code in an AdminPostController that lives in `src/controllers/admin`, you can render the results of an action to a template in `src/views/admin/products` this way:

```
render "show.slang", path: "views/products"
```

### Ways to render

```
render "index.slang" path: "views/admin/books"
render "show.slang", layout: "new_layout.slang")
render "edit.html.erb" path: "books"
render partial: "_form.slang"
```

### The :template Option

This is the view template you would like to render, this could be of `slang`, `ecr`, `mustache`, or `temel`

```
render template: "edit.ecr"
render template: "edit.html.ecr"
render template: "edit.html.slang"
```

### The :layout Option

With most of the options to render, the rendered content is displayed as part of the current layout. You'll learn more about layouts and how to use them later in this guide.

You can use the :layout option to tell Rails to use a specific file as the layout for the current action:

```
render("show.slang", layout: "new_layout.slang")
```

You can also tell Rails to render with no layout at all:

```
render("show.slang", layout: false)
```

#### Examples

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


# Layouts

### Finding Layouts

To find the current layout, Amber first looks for a file in `src/views/layouts` with the same base name as the controller. For example, rendering actions from the PhotosController class will use `src/views/layouts/photos.ecr`. If there is no such controller-specific layout, Amber will use `src/views/layouts/application.html.ecr`. 

Amber also provides several ways to more precisely assign specific layouts to individual controllers and actions.

### Specifying Layouts for Controllers

You can override the default layout conventions in your controllers by using the layout declaration. For example:

```
class ProductsController < ApplicationController
  LAYOUT = "inventory.slang"
  #...
end
```

### Passing data from Controllers to Views

All local variables assigned in the controller action are available in the views. This is because the views are compiled in the same scope as the controller action. There is no copying or passing data between the view and controller like other frameworks. This keeps memory consumption lower and improves performance significantly.

# Using Partials

Partial templates - usually just called "partials" - are another device for breaking the rendering process into more manageable chunks. With a partial, you can move the code for rendering a particular piece of a response to its own file.

### Naming Partials

To render a partial as part of a view, you use the render method within the view:

```<%= render "menu" %>```

This will render a file named `_menu.html.ecr` at that point within the view being rendered. Note the leading underscore character: partials are named with a leading underscore to distinguish them from regular views, even though they are referred to without the underscore. This holds true even when you're pulling in a partial from another folder:

```<%= render "shared/menu.html.ecr" %>```

That code will pull in the partial from ```app/views/shared/_menu.html.ecr```.

### Using Partials to Simplify Views

One way to use partials is to treat them as the equivalent of subroutines: as a way to move details out of a view so that you can grasp what's going on more easily. For example, you might have a view that looked like this:

```
<%= render "shared/ad_banner" %>
<h1>Products</h1>
<p>Here are a few of our fine products:</p>
...
 
<%= render "shared/footer" %>
``` 

Here, the _ad_banner.html.ecr and _footer.html.ecr partials could contain content that is shared by many pages in your application. You don't need to see the details of these sections when you're concentrating on a particular page.
