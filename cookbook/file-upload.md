# File Upload

File uploading is very simple. Files are detected and turned into `Amber::Router::File` and put into a temporary file cache.

You can access files from the `params` macro using the `files` method.

```crystal
params.files
```

This will return a hash-like object of `Amber::Router::File` that you can work with. The attributes you can access are:

```crystal
file : ::File
filename : String?
headers : HTTP::Headers
creation_time : Time?
modification_time : Time?
read_time : Time?
size : UInt64?
```

For example, let's say we have a controller with a `create` method that we want someone to `POST` a JSON body that includes a file to upload:

```crystal
# header: `accepts: audio/mp3`
#
# POST body
# {
#    "audio_file": // binary data for the audio file
#    "file_name": "some_file_name.mp3"
# }
def create
  uploaded_file = params.files["audio_file"]
  uploaded_file_name = params["file_name"]
  
  # Do whatever you want with the files and your method 😄
end
```

{% hint style="warning" %}
First you need an amber project generated with [Amber CLI](../guides/create-new-app.md) or [from scratch](from-scratch.md).
{% endhint %}

You still require a form to upload your file, see [views](../guides/views/). Also see [request and response](../guides/controllers/request-and-response-objects.md).
