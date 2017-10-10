# Websockets

Websockets allow instant communication between clients for building applications like chat apps, multiplayer games, live tickers or other forms of real time featured interactions. Much like Node Socket.io, Rails ActionCable, or Phoenix Channels, Amber websockets is based around messages and topics.

Amber websockets API is simple and only requires 3 working parts: A ClientSocket, a Channel and a client connection.

To get started create a socket endpoint within a pipeline with a url and your socket class:

```crystal
routes :api do
  websocket "/chat", UserSocket
end
```

### Pub/Sub

Tapping into the Redis PubSub feature allows applications to scale beyond a single server into an entire application cluster.  All messages are published to a single Redis source so you application can scale easily.