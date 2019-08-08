# File Upload

This recipe will help you to setup a basic file upload action in your application.

{% hint style="warning" %}
First you need an amber project generated with [Amber CLI](../guides/create-new-app.md) or [from scratch](from-scratch.md).
{% endhint %}

{% code-tabs %}
{% code-tabs-item title="src/controllers/some\_controller.cr" %}
```ruby
class SomeController < ApplicationController
  def file_upload
    image = params.files["image1"]
    filename = image.filename
    # Be sure to check if image.filename is not empty
    # otherwise it'll raise a compile time error
    if !filename.is_a?(String)
      "No filename included in upload"
    else
      File.rename(image.file.path, "some/static/resources/#{filename}")
      "Upload ok"
    end
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Then in your routes file:

{% code-tabs %}
{% code-tabs-item title="config/routes.cr" %}
```ruby
Amber::Server.configure do |app|
  pipeline :web do
    # pipelines...
  end

  routes :web do
    # other routes,,,
    post "/file_upload", SomeController, :file_upload
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You still require a form to upload your file, see [views](../guides/views/). Also see [request and response](../guides/controllers/request-and-response-objects.md).

Example slang file:

{% code-tabs %}
{% code-tabs-item title="src/controllers/some\_controller.cr" %}
```slang
form action="/some/file_upload" method="post" enctype="multipart/form-data"
  == csrf_tag 
  input type="file" name="file"
  button type="Submit" Save
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Special thanks to [Kemal Cookbook](http://kemalcr.com/cookbook/file_upload/) and [Nocturne Project](https://github.com/TheNocturneProject/Nocturne/blob/0d764e2ff15a1e200ee4ebe80614f4e560e78628/src/controllers/model_controllers/resource_controller.cr#L23-L28).



