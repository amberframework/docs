# Params Validation

```crystal
# Update Controller Action
def update
  update_params = params.validation do
    required(:email) { |p| p.url? }
    required(:age, UInt32)
  end
  user = User.new update_params.validate!
end
```



