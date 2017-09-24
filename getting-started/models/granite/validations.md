### Errors

All database errors are added to the `errors` array used by Granite::ORM::Validators with the symbol ':base'

```crystal
post = Post.new
post.save
post.errors[0].to_s.should eq "ERROR: name cannot be null"
```
