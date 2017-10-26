# View Helpers

The [Jasper::Helpers](https://github.com/amberframework/jasper-helpers) library provides a common set of helper methods that can simplify the development of the views.

## Links
A `link_to` helper is available:

```slim
== link_to "Home", "/"
```

Produces the following HTML
```html
<a href="/">Home</a>
```

## Buttons
A `button_to` helper is available:

```slim
== button_to "Logout", "/logout"
```

Produces the following HTML
```html
<form action="/logout" class="button" method="post">
  <button type="submit">Logout</button>
</form>
```
For more complex forms, see section below.

## Forms
A `form` helper is available, along with helpers for the following form elements:
- `text_field`
- `label`
- `hidden_field`
- `select_field`
- `text_area`
- `check_box`
- `submit`

Use `amber generate scaffold [Resource] [field:type] ...` to get the most up-to-date examples of using helpers for resources.

### text_field
```slim
/ create action
== form(action: "/posts", method: :post) do
  == text_field name: "title", value: "", placeholder: "Title"
  == submit("Create Post")

/ update action
/ This takes care of the hidden '_method' field
== form(action: "/posts", method: :patch) do
  == text_field name: "title", value: "", placeholder: "Title"
  == submit("Update Post")
```

Produces the following HTML
```html
<form action="/posts" method="post">
  <input type="text" name="title" id="title" value="" placeholder="Title">
  <input type="submit" value="Create Post" id="create_post">
</form>

<form action="/posts" method="post">
  <input type="hidden" name="_method" id="_method" value="patch">
  <input type="text" name="title" id="title" value="" placeholder="Title">
  <input type="submit" value="Update Post" id="update_post">
</form>
```

### label
```slim
== form(action: "/posts", method: :post) do
  == label :title
  == text_field name: "title", value: "", placeholder: "Title"
  == submit("Create Post")
```

Produces the following HTML
```html
<form action="/posts" method="post">
  <label for="title" id="title_label">Title</label>
  <input type="text" name="title" id="title" value="" placeholder="Title">
  <input type="submit" value="Create Post" id="create_post">
</form>
```

### text_area
```slim
== form(action: "/posts", method: :post) do
  == text_area name: "body", content: "", placeholder: "Body", size: "30x10"
  == submit("Create Post")
```

Produces the following HTML
```html
<form action="/posts" method="post">
  <textarea name="body" id="body" placeholder="Body" cols="30" rows="10"></textarea>
  <input type="submit" value="Create Post" id="create_post">
</form>
```

### hidden_field
```slim
== form(action: "/posts", method: :post) do
  == hidden_field name: "secret", content: "Super Secret"
  == submit("Create Post")
```

Produces the following HTML
```html
<form action="/posts" method="post">
  <input type="hidden" name="secret" id="secret" content="Super Secret">
  <input type="submit" value="Create Post" id="create_post">
</form>
```

### select_field
```slim
/ Array of Arrays
== form(action: "/posts", method: :post) do
  == label :ranking
  == select_field name: "ranking", collection: [[1, "First"], [2, "Second"]], selected: 1

/ Array of Hashes
== form(action: "/posts", method: :post) do
  == label :ranking
  == select_field name: "ranking", collection: [{ 1 => "First" }, { 2 => "Second" }], selected: 1

/ Hash
== form(action: "/posts", method: :post) do
  == label :ranking
  == select_field name: "ranking", collection: { 1 => "First", 2 => "Second" }, selected: 1
```

All the previous code samples produce the following HTML
```html
<form action="/posts" method="post">
  <label for="ranking" id="ranking_label">Ranking</label>
  <select name="ranking">
    <option value="1" selected="selected">First</option>
    <option value="2">Second</option>
  </select>
</form>
```

### check_box
```slim
== form(action: "/posts", method: :post) do
  == label(:published)
  == check_box(:published, checked: false)
```

Produces the following HTML
```html
<form action="/posts" method="post">
  <label for="published" id="published_label">Published</label>
  <input type="hidden" name="published" id="published" value="0">
  <input type="checkbox" name="published" id="published" value="1" checked="false">
</form>
```

### All together
```slim
== form(action: "/posts", method: :post) do
  == hidden_field name: "secret", content: "Super Secret"
  == label :title
  == text_field name: "title", value: "", placeholder: "Title"
  == label :body
  == text_area name: "body", content: "", placeholder: "Body", size: "30x10"
  == label :ranking
  == select_field name: "ranking", collection: [[1, "First"], [2, "Second"]], selected: 1
  == label(:published)
  == check_box(:published, checked: false)

  == submit("Create Post")
```

Produces the following HTML
```html
<form action="/posts" method="post">
  <input type="hidden" name="secret" id="secret" content="Super Secret">
  <label for="title" id="title_label">Title</label>
  <input type="text" name="title" id="title" value="" placeholder="Title">
  <label for="body" id="body_label">Body</label>
  <textarea name="body" id="body" placeholder="Body" cols="30" rows="10"></textarea>
  <label for="ranking" id="ranking_label">Ranking</label>
  <select name="ranking">
    <option value="1" selected="selected">First</option>
    <option value="2">Second</option>
  </select>
  <label for="published" id="published_label">Published</label>
  <input type="hidden" name="published" id="published" value="0"><input type="checkbox" name="published" id="published" value="1" checked="false">
  <input type="submit" value="Create Post" id="create_post">
</form>
```
