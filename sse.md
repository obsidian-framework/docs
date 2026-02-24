# Server-Sent Events (SSE)

SSE enables real-time server-to-client streaming over plain HTTP. Perfect for notifications, live dashboards, and activity feeds. Automatic reconnection is handled by the browser.

## Basic SSE Endpoint

```java
@Controller
public class NotificationController {

    @SSE(value = "/notifications/stream", name = "notifications.stream")
    public Object stream(Request req, Response res) {
        res.raw().setCharacterEncoding("UTF-8");
        PrintWriter writer = res.raw().getWriter();

        try {
            for (int i = 0; i < 10; i++) {
                String notification = String.format(
                    "{\"type\":\"Info\",\"message\":\"Event %d\"}",
                    i
                );

                writer.write("data: " + notification + "\n\n");
                writer.flush();

                Thread.sleep(5000);
            }
        } catch (Exception e) {
            // Client disconnected
        } finally {
            writer.close();
        }

        return null;
    }
}
```

> **Important:** Always set UTF-8 encoding to avoid charset issues: `res.raw().setCharacterEncoding("UTF-8");`

## Client-Side JavaScript

```javascript
const eventSource = new EventSource('/notifications/stream');

eventSource.onmessage = (event) => {
    const data = JSON.parse(event.data);
    console.log('Notification:', data);
};

eventSource.onerror = (error) => {
    console.error('Connection error');
    // Automatically reconnects
};

// Close when done
eventSource.close();
```

## Message Format

SSE messages follow a simple text protocol. Each event must end with two newlines (`\n\n`):

```java
// Simple text
writer.write("data: Hello World\n\n");

// JSON (recommended)
writer.write("data: {\"user\":\"Alice\",\"message\":\"Hi!\"}\n\n");

// Named event (optional)
writer.write("event: notification\n");
writer.write("data: {\"text\":\"New message\"}\n\n");
```
