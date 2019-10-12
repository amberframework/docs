# Form Builder

You may find yourself looking for a more advanced way to build your HTML forms that is both simple and productive. For this we recommend the optional shard [FormBuilder.cr](https://github.com/westonganger/form_builder.cr)

## Features of FormBuilder.cr

- Easily generate HTML markup for forms, labels, inputs, and help text
- Support for error messages for your validations
- Creates markup for the following UI libraries out of the box such as Bootstrap (v2-v4), Bulma, Foundation, Materialize, Milligram & Semantic UI
- Custom theme support

## Installation

Because this is an optional addon you must install and require it in your Amber project.

Add this to your application's `shard.yml` file:

```yaml
dependencies:
  form_builder:
    github: westonganger/form_builder.cr
```

Then require it within the `config/application.cr` file:

```crystal
require "form_builder"
```

## Usage

The following field types are supported:

- `:checkbox`
- `:file`
- `:hidden`
- `:password`
- `:radio`
- `:select`
- `:text`
- `:textarea`

## FormBuilder in View Templates (Example in Slang)

```crystal
== FormBuilder.form(theme: :bootstrap_4_vertical, action: "/products", method: :post, form_html: {style: "margin-top: 20px;", "data-foo" => "bar"}) do |f|
  == csrf_tag
  
  .row.main-examples
    .col-sm-6
      ### -- Field Options
      ### type : (String | Symbol)
      ### name : (String | Symbol)?
      ### label : (String | Bool)? = true
      ### help_text : String?

      ### value : (String | Symbol)?
      ### -- Note: The `input_html["value"]` option will take precedence over the :value option (except for `type: :textarea/:select`)

      ### errors : (Array(String) | String)?
      ### -- Note: Array(String) generates a list of help text elements. If you have an Array of errors and you only want a single help text element, then join your errors array to a String

      ### -- For the following Hash options, String keys will take precedence over any Symbol keys
      ### input_html : (Hash | NamedTuple)? ### contains attributes to be added to the input/field
      ### label_html : (Hash | NamedTuple)? ### contains attributes to be added to the label
      ### wrapper_html : (Hash | NamedTuple)? ### contains attributes to be added to the outer wrapper for the label and input
      ### help_text_html : (Hash | NamedTuple)? ### contains attributes to be added to the help text container
      ### error_html : (Hash | NamedTuple)? ### contains attributes to be added to the error container(s) 
 
      == f.field name: "product[name]", label: "Name", type: :text, errors: product_errors["name"]

      == f.field name: "product[description]", label: "Description", type: :textarea, input_html: {class: "foobar"}, wrapper_html: {style: "margin-top: 10px"}, label_html: {style: "color: red;"}

      == f.field name: "product[file]", type: :file, help_text: "Must be a PDF", help_text_html: {style: "color: blue;"}

    .col-sm-6
      == f.field name: "product[available]", type: :checkbox, label: "In Stock?"

      == f.field name: "product[class]", type: :radio, label: false

      == f.field name: "product[secret]", type: :hidden, value: "foobar"

  .row.select-example
    ### -- Additional Options for `type: :select`
    ### collection : (Hash | NamedTuple) = {
    ###   options : (Array(String) | Array(String | Array(String)) | String) ### Required, Note: The non-Array String type is for passing in a pre-built html options string
    ###   selected : (String | Array(String))?
    ###   disabled : (String | Array(String))?
    ###   include_blank : (String | Bool)?
    ### }
    ### -- Note: String keys will take precedence over any Symbol keys

    ### -- When passing a nested array to collection[:options] the Option pairs are defined as: [required_value, optional_label]
    - opts = [["A", "Type A"], ["B" "Type B"], ["C", "Type C"], "Other"]

    == f.field name: "product[type]", label: "Type", type: :select, collection: {options: opts, selected: ["B"], disabled: ["C"]}
```

## FormBuilder in Plain Crystal Code

When using the `FormBuilder.form` method in plain Crystal code, the `<<` syntax is required to add the generated field HTML to the form HTML string

```crystal
form_html_str = FormBuilder.form(theme: :bootstrap_4_vertical, action: "/products", method: :post, form_html: {style: "margin-top: 20px;", "data-foo" => "bar"}) do |f|
  f << csrf_tag
  f << f.field(name: "name", type: :text, label: "Name")
  f << f.field(name: "sku", type: :text, label: "SKU")
  f << "<strong>Hello World</strong>"
end
```

OR you can use the lower level `String.build` instead:

```crystal
form_html_str = String.build do |str|
  str << FormBuilder.form(theme: :bootstrap_4_vertical, action: "/products", method: :post, form_html: {style: "margin-top: 20px;", "data-foo" => "bar"}) do |f|
    str << csrf_tag
    str << f.field(name: "name", type: :text, label: "Name")
    str << f.field(name: "sku", type: :text, label: "SKU")
    str << "<strong>Hello World</strong>"
  end
end
```

## FormBuilder without a Form

```crystal
- f = FormBuilder::Builder.new(theme: :bootstrap_4_vertical)

== f.field name: "name", type: :text, label: "Name"
== f.field name: "sku", type: :text, label: "SKU"
```

## Error Handling

The form builder is capable of handling error messages too. If the `:errors` argument is provided it will generate the appropriate error help text element(s) next to the field.

```crystal
== FormBuilder.form(theme: :bootstrap_4_vertical) do |f|
  == csrf_tag

  == f.field name: "name", type: :text, label: "Name", errors: "cannot be blank"
  == f.field name: "sku", type: :text, label: "SKU", errors: ["must be unique", "incorrect SKU format")
```

## Theme List

Out of the box Form Builder can generate HTML markup for a variety of popular UI libraries. Custom Themes are also supported, please see the [FormBuilder.cr README](https://github.com/westonganger/form_builder.cr) for details.

The current list of built-in themes are as follows.

- Bootstrap 4 
  * `theme: :bootstrap_4_vertical`
  * `theme: :bootstrap_4_inline`
  * `theme: :bootstrap_4_horizontal` or `theme: FormBuilder::Themes::Bootstrap4Horizontal.new(column_classes: ["col-sm-3","col-sm-9"])`
- Bootstrap 3
  * `theme: :bootstrap_3_vertical`
  * `theme: :bootstrap_3_inline`
  * `theme: :bootstrap_3_horizontal` or `theme: FormBuilder::Themes::Bootstrap3Horizontal.new(column_classes: ["col-sm-3","col-sm-9"])`
- Bootstrap 2
  * `theme: :bootstrap_2_vertical`
  * `theme: :bootstrap_2_inline`
  * `theme: :bootstrap_2_horizontal`
- Bulma
  * `theme: :bulma_vertical`
  * `theme: :bulma_horizontal`
- Foundation
  * `theme: :foundation`
- Materialize
  * `theme: :materialize`
- Milligram
  * `theme: :milligram`
- Semantic UI
  * `theme: :semantic_ui_vertical`
  * `theme: :semantic_ui_inline`
- None (Default)
  * `theme: :default`
  * `theme: nil`
  * or simply do not provide a `:theme` argument

For further information please see the official [FormBuilder.cr README](https://github.com/westonganger/form_builder.cr)
