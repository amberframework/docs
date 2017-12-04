# Websockets

Websockets allow instant communication between clients for building applications like chat apps, multiplayer games, live tickers or other forms of real time featured interactions. Much like Node Socket.io, Rails ActionCable, or Phoenix Channels, Amber websockets is based around messages and topics.

You can try a minimal working example see [Ambrockets](https://ambrockets.herokuapp.com/).

[![ambrockets](https://raw.githubusercontent.com/faustinoaq/ambrockets/master/src/assets/images/logo.png)](https://ambrockets.herokuapp.com)

Amber websockets API is simple and only requires 3 working parts: A ClientSocket, a Channel and a client connection.

To get started create a socket endpoint within a pipeline with a url and your socket class:

```crystal
routes :web do
  websocket "/chat", ChatSocket
end
```

### Pub/Sub

Tapping into the Redis PubSub feature allows applications to scale beyond a single server into an entire application cluster.  All messages are published to a single Redis source so you application can scale easily.