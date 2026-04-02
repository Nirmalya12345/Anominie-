# Anominie Backend Server

This is the backend server for the Anominie anonymous chat application, built with Node.js, Express, and Socket.IO.

## Features

- Real-time WebSocket communication with Socket.IO
- User session management
- Online user tracking
- Global chat room
- Typing indicators
- Join/leave notifications
- CORS support for cross-origin requests

## Installation

```bash
npm install
```

## Environment Variables

Create a `.env` file in this directory:

```env
PORT=3000
NODE_ENV=development
FRONTEND_URL=http://localhost:8000
```

For production:
```env
PORT=3000
NODE_ENV=production
FRONTEND_URL=https://your-frontend-url.vercel.app
```

## Running the Server

### Development
```bash
npm start
```

The server will run on `http://localhost:3000`

### Production
Ensure environment variables are set for production, then:
```bash
npm start
```

## API Endpoints

### REST API

#### GET /
Returns server status and online user count.

**Response:**
```json
{
  "message": "Anominie Chat Server",
  "online": 5
}
```

#### GET /api/stats
Returns detailed statistics.

**Response:**
```json
{
  "onlineUsers": 5,
  "totalConnections": 5
}
```

## WebSocket Events

### Client → Server Events

#### `user_login`
User logs in with credentials.

**Payload:**
```javascript
{
  username: "JohnDoe",
  country: "USA",
  email: "john@example.com",  // optional
  phone: "+1234567890"         // optional
}
```

#### `chat_message`
Send a chat message to all users.

**Payload:**
```javascript
{
  message: "Hello everyone!"
}
```

#### `typing`
Notify others that user is typing.

**Payload:** None

#### `stop_typing`
Notify others that user stopped typing.

**Payload:** None

#### `disconnect`
User disconnects (automatic event).

**Payload:** None

### Server → Client Events

#### `online_count`
Sent when a user connects to inform them of current online count.

**Payload:**
```javascript
{
  count: 5
}
```

#### `system_message`
System notification message.

**Payload:**
```javascript
{
  message: "Welcome to Anominie!",
  timestamp: "2026-04-02T12:00:00.000Z"
}
```

#### `user_joined`
Broadcast when a user joins the chat.

**Payload:**
```javascript
{
  username: "JohnDoe",
  country: "USA",
  onlineCount: 6
}
```

#### `user_left`
Broadcast when a user leaves the chat.

**Payload:**
```javascript
{
  username: "JohnDoe",
  onlineCount: 5
}
```

#### `new_message`
Broadcast a new chat message to all users.

**Payload:**
```javascript
{
  id: "1234567890",
  username: "JohnDoe",
  country: "USA",
  message: "Hello everyone!",
  timestamp: "2026-04-02T12:00:00.000Z"
}
```

#### `user_typing`
Someone is typing.

**Payload:**
```javascript
{
  username: "JohnDoe"
}
```

#### `user_stop_typing`
User stopped typing.

**Payload:** None

## Data Storage

This server uses **in-memory storage** for user sessions:
- No database required
- User data is lost on server restart
- Messages are not persisted (privacy by design)

For production with persistence, you would need to:
1. Add MongoDB or PostgreSQL
2. Implement message history storage
3. Add user authentication with JWT
4. Store chat logs (optional)

## Security

- CORS configured for allowed origins
- Input validation (client-side)
- XSS protection (client-side escaping)
- No sensitive data storage
- Environment variables for configuration

## Development

### Testing the Server

1. Start the server:
   ```bash
   npm start
   ```

2. Open browser console and test with Socket.IO client:
   ```javascript
   const socket = io('http://localhost:3000');

   socket.on('connect', () => {
     console.log('Connected!');
     socket.emit('user_login', {
       username: 'TestUser',
       country: 'USA'
     });
   });

   socket.on('online_count', (data) => {
     console.log('Online users:', data.count);
   });
   ```

### Logs

The server logs important events:
- Client connections/disconnections
- User logins
- Messages sent
- Errors

Example log output:
```
Server running at http://localhost:3000
Environment: development
New client connected: abc123
User logged in: JohnDoe (USA)
Message from JohnDoe: Hello!
User disconnected: JohnDoe
Client disconnected: abc123
```

## Deployment

See [DEPLOYMENT.md](../DEPLOYMENT.md) in the root directory for deployment instructions.

### Quick Deploy Options

**Render.com:**
- Root Directory: `backend`
- Build Command: `npm install`
- Start Command: `npm start`

**Railway.app:**
- Root Directory: `/backend`
- Auto-detected Node.js app

**Heroku:**
```bash
cd backend
heroku create anominie-backend
git push heroku main
```

## Dependencies

```json
{
  "express": "^4.18.2",        // Web framework
  "socket.io": "^4.6.1",       // WebSocket library
  "cors": "^2.8.5",            // CORS middleware
  "dotenv": "^16.0.3",         // Environment variables
  "jsonwebtoken": "^9.0.2"     // JWT (for future auth)
}
```

## Troubleshooting

### Port already in use
Change the `PORT` in `.env` or stop other services using port 3000.

### CORS errors
Update `FRONTEND_URL` in `.env` to match your frontend URL.

### Socket.IO connection fails
- Ensure server is running
- Check firewall settings
- Verify WebSocket is not blocked
- Check browser console for errors

## Future Enhancements

- [ ] Add MongoDB for message persistence
- [ ] Implement JWT authentication
- [ ] Add private messaging
- [ ] Add chat rooms/channels
- [ ] Add message reactions
- [ ] Add file/image sharing
- [ ] Add user blocking
- [ ] Add rate limiting
- [ ] Add admin moderation tools
- [ ] Add message encryption

## License

MIT

## Support

For issues, please check the main repository's issue tracker.
