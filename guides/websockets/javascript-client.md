# JavaScript Client

## Example Usage

For use with web based applications, a JavaScript library \(`amber.js`\) is included.

### Create a single connection to the server

The first step in instantiating a socket connection with the server through JavaScript, is by using the library 
provided by Amber. Place the route that was created on the server side in the `routes.cr` as the endpoint to which
the front end will connect to.

```javascript
let socket = new Amber.Socket('/chat')
socket.connect() # returns a promise
  .then(() => {})
```

### Join a channel

After the promise passes, include the following code in the function from within the `.then` that will be triggered.

```javascript
let channel = socket.channel('chat_room:123')
channel.join()
```

In the above example, `chat_room` is the channel that was created on line 2 of the `ChatSocket` struct. You can dynamically
create separate channels within the prefix of `chat_room:` by appending any character after the colon. This allows for the 
creation of chatrooms that will allow different clients to connect to it.

### Send events / messages

When you need to send a message after a user submits the form that includes their message, you'll want to push it 
out to the server for it to be rebroadcasted to all connected clients.

```javascript
channel.push('message_new', { message: 'amber websockets is the bomb diggity!' })
```

### Subscribe to events / messages

After a message is sent, you'll need to have a listener that listens to a specific subject from within the socket channel.
For example, below the subject of `message_new` is being listened to from within the `chat_room:123` channel. You may also 
have a listener on the subject of `user_join` to notify active connections of a new user to the chatroom.

```javascript
channel.on('message_new', (message) => {
  // handle new message here
})

channel.on('user_join', (message) => {})
```
