# Directory Structure

This will generate a traditional web application:

* **/config** - Application and HTTP::Handler config's goes here. The database.yml and routes.cr are here.
* **/lib** - shards are installed here.
* **/public** - Default location for html/css/js files. The static handler points to this directory.
* **/spec** - all the crystal specs go here.
* **/src** - all the source code goes here, controllers, views, models, presenter, etc.

Generate scaffolding for a resource:

```
amber generate scaffold Post name:string body:text draft:bool
```

This will generate scaffolding for a Post:

* src/controllers/post\_controller.cr
* src/models/post.cr
* src/views/post/\*
* db/migrations/\[datetimestamp\]\_create\_post.sql
* spec/controllers/post\_controller\_spec.cr
* spec/models/post\_spec.cr
* appends route to config/routes.cr
* appends navigation to src/layouts/\_nav.slang



