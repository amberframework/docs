# Websockets

Websockets allow full-duplex instant communication between clients, for solutions such as chat or other forms of real time communication.  The websockets amber websockets API is very simple, and only requires a Socket, a Channel, and client side interaction using javascript.

#### Channels

Users will subscribe and unsubscribe from channels.  Channels define 3 public methods that can be used:

* `handle_joined` - Called when a user joins a channel
* `handle_message` - Called when a user sends a message to a channel.  A common message handler will simply rebroadcast the message to the other subscribers with `rebroadcast!` method.
* `handle_leave` - Called when the user leaves the channel.

```ruby
class ChatRoomChannel < Amber::Websockets::Channel

  # optional
  def handle_joined
    # channel join related functionality
  end
  
  # required
  def handle_message(msg)
    rebroadcast!(msg)
  end
  
  # optional
  def handle_leave
    # channel leave functionality    
  end
end
```



#### Sockets

Socket structs are the object that is stored in memory per connection and retain the persistent communication.  Sockets define one public method `on_connect` which can contain functionality that should run when a user connects, including authentication.  This methods should return a Bool.  If `true`, the socket will remain open.  If `false`, the socket will be closed immediately.

Socket structs also map topics to the channels they will connect with

```ruby
struct UserSocket < Amber::WebSockets::ClientSocket
  channel "chat_room:*", ChatRoomChannel
  
  def on_connect
    # do authentication here
    true
  end
end
```



