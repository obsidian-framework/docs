# WebSockets

WebSocket handlers enable bi-directional real-time communication between client and server. Perfect for chat applications, live collaboration, and gaming.

## Basic Handler

```java
@org.eclipse.jetty.websocket.api.annotations.WebSocket
@WebSocket("/ws/chat")
public class ChatHandler {

    private static final Map<Session, String> users = new ConcurrentHashMap<>();

    @OnWebSocketConnect
    public void onConnect(Session session) throws IOException {
        session.getRemote().sendString("Welcome to the chat!");
    }

    @OnWebSocketMessage
    public void onMessage(Session session, String message) throws IOException {
        // Broadcast to all connected users
        for (Session s : users.keySet()) {
            if (s.isOpen()) {
                s.getRemote().sendString(message);
            }
        }
    }

    @OnWebSocketClose
    public void onClose(Session session, int code, String reason) {
        users.remove(session);
    }
}
```

## Client-Side JavaScript

```javascript
const ws = new WebSocket('ws://localhost:8888/ws/chat');

ws.onopen = () => {
    console.log('Connected!');
    ws.send('Hello server!');
};

ws.onmessage = (event) => {
    console.log('Received:', event.data);
};

ws.onclose = () => {
    console.log('Disconnected');
};
```

## Path Convention

Use the `/ws/` prefix for WebSocket endpoints to avoid conflicts with HTTP routes:

```java
// ✅ Good — no conflict
@WebSocket("/ws/chat")  // WebSocket endpoint
@GET("/chat")           // HTTP route (serves HTML page)

// ❌ Bad — path conflict
@WebSocket("/chat")     // ⚠️ Conflicts with HTTP route
@GET("/chat")           // ⚠️ Conflicts with WebSocket
```

## Required Annotations

WebSocket handlers must carry **both** annotations:

- `@WebSocket("/path")` — Obsidian annotation for route registration
- `@org.eclipse.jetty.websocket.api.annotations.WebSocket` — Jetty annotation for handler discovery

> **Note:** WebSocket handler classes should be placed in the `websockets` subpackage of your application (e.g. `com.yourcompany.yourapp.websockets`).
