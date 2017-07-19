# Responses

Response objects are delivered by actions, methods which return a string response, which is your response.

```crystal
def my_action
  "my response"
end
```

To return JSON, the easiest way is to create a hash and call `.to_json` on it

```crystal
def my_action
  {title: "thing", body: "lorem ipsum dolor sit amet adipisci"}.to_json
end
```



