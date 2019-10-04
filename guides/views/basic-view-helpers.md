# Basic View Helpers

The [Jasper::Helpers](https://github.com/amberframework/jasper-helpers) library provides a common set of helper methods that can simplify the development of the views.

## Links

A `link_to` helper is available:

```text
== link_to "Home", "/"
```

Produces the following HTML

```markup
<a href="/">Home</a>
```

## Buttons

A `button_to` helper is available:

```text
== button_to "Logout", "/logout"
```

Produces the following HTML

```markup
<form action="/logout" class="button" method="post">
  <button type="submit">Logout</button>
</form>
```

For more complex forms, see section below.

## Forms

The form helpers listed below are very basic helpers that are included by default. For a more complete form building experience we strongly recommend using [FormBuilder.cr](form-builder.md)

The following methods provide simple HTML form elements:

* `form`
* `text_field`
* `label`
* `hidden_field`
* `select_field`
* `text_area`
* `check_box`
* `submit`

Use `amber generate scaffold [Resource] [field:type] ...` to get the most up-to-date examples of using helpers for resources.

### form

```text
== form(action: "/posts", method: :post) do
  == csrf_tag
  == submit("Create Post")

/ When using `method: :patch`, it add the hidden '_method' field for you
== form(action: "/posts", method: :patch) do
  == csrf_tag
  == submit("Update Post")
```

Produces the following HTML

```markup
<form action="/posts" method="post">
  <input type="hidden" name="<csrf-name-here>" value="<csrf-token-here>" />
  <input type="submit" value="Create Post" id="create_post">
</form>

<form action="/posts" method="post">
  <input type="hidden" name="_method" id="_method" value="patch">
  <input type="hidden" name="<csrf-name-here>" value="<csrf-token-here>" />
  <input type="submit" value="Update Post" id="update_post">
</form>
```

### text\_field

```text
== text_field name: "title", value: "", placeholder: "Title"
```

Produces the following HTML

```markup
<input type="text" name="title" id="title" value="" placeholder="Title">
```

### label

```text
== label :title
```

Produces the following HTML

```markup
<label for="title" id="title_label">Title</label>
```

### text\_area

```text
== text_area name: "body", content: "", placeholder: "Body", size: "30x10"
```

Produces the following HTML

```markup
<textarea name="body" id="body" placeholder="Body" cols="30" rows="10"></textarea>
```

### hidden\_field

```text
== hidden_field name: "secret", content: "Super Secret"
```

Produces the following HTML

```markup
<input type="hidden" name="secret" id="secret" content="Super Secret">
```

### select\_field

```text
/ Array of Arrays
== select_field name: "ranking", collection: [[1, "First"], [2, "Second"]], selected: 1

/ Array of Hashes
== select_field name: "ranking", collection: [{ 1 => "First" }, { 2 => "Second" }], selected: 1

/ Hash
== select_field name: "ranking", collection: { 1 => "First", 2 => "Second" }, selected: 1
```

All the previous code samples produce the following HTML

```markup
<select name="ranking">
  <option value="1" selected="selected">First</option>
  <option value="2">Second</option>
</select>
```

### check\_box

```text
== check_box(:published, checked: false)
```

Produces the following HTML

```markup
<input type="checkbox" name="published" id="published" value="1" checked="false">
```

### All together

```text
== form(action: "/posts", method: :post) do
  == csrf_tag

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

```markup
<form action="/posts" method="post">
  <input type="hidden" name="<csrf-name-here>" value="<csrf-token-here>" />

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
