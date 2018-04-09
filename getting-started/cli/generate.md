# Generate Command

The generators in amber are a great way to get an application up and running quickly. In addition, they help keep code consistent and following convention.

## Usage

You can always see the available generators by running `amber generate` as below:
```shell
$ amber generate
Parsing Error: The TYPE argument is required.

amber generate [OPTIONS] TYPE NAME [FIELDS1 FIELDS2...]

Arguments:
  FIELDS (multiple)  user:reference name:string body:text age:integer published:bool
  NAME               name of resource
  TYPE               scaffold, model, controller, migration, mailer, socket, channel, auth

Options:
  --no-color  Disable colored output
```
Generators can be used multi-word names, the class names will be `CamelCase` and file names will be `snake_case`, the command line input can be either:
```shell
$ amber generate [TYPE] MultiWordName [FIELD1 FIELD2 ...]
# OR
$ amber generate [TYPE] multi_word_name [FIELD1 FIELD2 ...]
```
See [Scaffolding](###scaffolding) below for full example.

Examples of Usage
- [Scaffolding](###scaffolding)
- [Auth](###auth)
- [Model](###model)
- [Controller](###controller)

### Scaffolding
Scaffolding will create the model (and specs), views, controller (and specs) and migrations for a resource. Additionally, it will add the new resource to the nav bar and
```shell
$ amber generate scaffold Post title body:text
Rendering Scaffold post
new       db/migrations/20171114103058417_create_post.sql
new       spec/models/spec_helper.cr
new       spec/models/post_spec.cr
new       src/models/post.cr
new       spec/controllers/spec_helper.cr
new       spec/controllers/post_controller_spec.cr
new       src/controllers/post_controller.cr
rewritten src/views/layouts/_nav.slang
new       src/views/post/_form.slang
new       src/views/post/edit.slang
new       src/views/post/index.slang
new       src/views/post/new.slang
new       src/views/post/show.slang
```
If you scaffold a multi-word resource, the class names will be `CamelCase` and file names will be `snake_case`, the command line input can be either:
```shell
$ amber generate scaffold PostComment post:reference body:text
# OR
$ amber generate scaffold post_comment post:reference body:text
Rendering Scaffold post_comment
new       db/migrations/20171114103306763_create_post_comment.sql
skipped   spec/models/spec_helper.cr
new       spec/models/post_comment_spec.cr
new       src/models/post_comment.cr
skipped   spec/controllers/spec_helper.cr
new       spec/controllers/post_comment_controller_spec.cr
new       src/controllers/post_comment_controller.cr
rewritten src/views/layouts/_nav.slang
new       src/views/post_comment/_form.slang
new       src/views/post_comment/edit.slang
new       src/views/post_comment/index.slang
new       src/views/post_comment/new.slang
new       src/views/post_comment/show.slang
```

### Auth

```shell
$ amber g auth User
Rendering Auth user
new       db/migrations/20171019214851_create_user.sql
new       db/seeds.cr
new       spec/controllers/spec_helper.cr
new       spec/models/spec_helper.cr
new       spec/models/user_spec.cr
new       src/controllers/registration_controller.cr
new       src/controllers/session_controller.cr
new       src/handlers/authenticate.cr
new       src/models/user.cr
rewritten src/views/layouts/_nav.slang
new       src/views/registration/new.slang
new       src/views/session/new.slang
```

### Model

```sh
$ amber g model Person name:string age:integer
Rendering Model person
new       db/migrations/20171019214940_create_person.sql
skipped   spec/models/spec_helper.cr
new       spec/models/person_spec.cr
new       src/models/person.cr
```

### Controller

```sh
$ amber g controller Person index:get show:get create:post update:patch
Rendering Controller person
skipped   spec/controllers/spec_helper.cr
new       spec/controllers/person_controller_spec.cr
new       src/controllers/person_controller.cr
```

## Recipes

Recipes are available to generate Scaffolding, Controller and Model artifacts
in ways that vary from the standard built in generator.  See the **Recipes**
option of the **Command Line Tool** for more information about using recipes to
generate applications.
