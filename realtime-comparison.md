# WebSocket vs SSE

Choose the right technology for your real-time needs.

## Feature Comparison

| Feature | WebSocket | SSE |
|---|---|---|
| **Direction** | Bi-directional (Client ↔ Server) | Uni-directional (Server → Client) |
| **Protocol** | WebSocket (`ws://`) | HTTP / HTTPS |
| **Reconnection** | Manual | Automatic |
| **Complexity** | Medium | Low |
| **Best for** | Chat, games, collaboration | Notifications, logs, dashboards |

## When to Use WebSocket

- The client needs to send data frequently
- Building a chat application
- Real-time games or collaboration tools
- You need full-duplex communication

## When to Use SSE

- Only the server pushes data to the client
- Building a notification system
- Live dashboards and metrics
- You want automatic reconnection without extra code

## Ready-to-Use Examples

A dedicated repository contains practical examples demonstrating both WebSocket and SSE implementations, including real-time notifications and chat systems.

→ [View realtime examples on GitHub](https://github.com/obsidian-framework/realtime-examples)
