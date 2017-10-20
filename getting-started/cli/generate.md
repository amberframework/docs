# Generate Command



## Usage


#### Scaffolding

```shell
amber g scaffold Post title:string body:text
Rendering Scaffold post
new       db/migrations/20170928115349_create_post.sql
new       spec/controllers/spec_helper.cr
new       spec/controllers/post_controller_spec.cr
new       spec/models/spec_helper.cr
new       spec/models/post_spec.cr
new       src/controllers/post_controller.cr
new       src/models/post.cr
rewritten src/views/layouts/_nav.slang
new       src/views/post/_form.slang
new       src/views/post/edit.slang
new       src/views/post/index.slang
new       src/views/post/new.slang
new       src/views/post/show.slang
```

#### Auth

```shell
amber g auth User
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

#### Model

```sh
amber g model Person name:string age:integer
Rendering Model person
new       db/migrations/20171019214940_create_person.sql
skipped   spec/models/spec_helper.cr
new       spec/models/person_spec.cr
new       src/models/person.cr
```

#### Controller

```sh
amber g controller Person index:get show:get create:post update:patch
Rendering Controller person
skipped   spec/controllers/spec_helper.cr
new       spec/controllers/person_controller_spec.cr
new       src/controllers/person_controller.cr
```
