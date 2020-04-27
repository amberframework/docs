# WebSocket Chat

This recipe will help you to setup a basic WebSocket chat in your application.

{% hint style="warning" %}
First you need an amber project generated with [Amber CLI](../guides/create-new-app.md) or [from scratch](from-scratch.md).
{% endhint %}

{% code-tabs %}
{% code-tabs-item title="src/channels/chat\_room\_channel.cr" %}
```ruby
class ChatRoomChannel < Amber::Websockets::Channel
  def handle_message(client_socket, msg)
    rebroadcast!(msg)
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Then setup your socket file:

{% code-tabs %}
{% code-tabs-item title="src/sockets/chat\_socket.cr" %}
```ruby
struct ChatSocket < Amber::WebSockets::ClientSocket
  channel "chat_room:*", ChatRoomChannel

  def on_connect
    # returning true accept all connections
    # you can use authentication here
    true
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Then add the `websocket` verb in your routes file:

{% code-tabs %}
{% code-tabs-item title="config/routes.cr" %}
```ruby
Amber::Server.configure do |app|
  pipeline :web do
    # pipelines...
  end

  routes :web do
    # other routes,,,
    websocket "/chat", ChatSocket
  end
end
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Finally you will require an [Amber JavaScript Client](../guides/websockets/javascript-client.md) to connect with your server:

{% hint style="info" %}
You can get `amber.min.js` from `lib/amber/assets/js/amber.min.js`
{% endhint %}

{% code-tabs %}
{% code-tabs-item title="public/index.html" %}
```markup
<script src="/public/amber.min.js"></script>
<script>
let socket = new Amber.Socket('/chat');

socket.connect().then(function() {
    let channel = socket.channel('chat_room:hello');

    channel.join();

    channel.push('message_new', {
      message: "Hello Amber from WebSocket Client!"
    });

    channel.on('message_new', function(payload) {
      console.log(payload);
    });
});
</script>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="warning" %}
To serve static files you must enable `:static` routes, please see [pipelines](../guides/routing/pipelines.md).
{% endhint %}

Also see more detailed information about this in[ ](../guides/controllers/cookies.md)[WebSockets](../guides/websockets/).

