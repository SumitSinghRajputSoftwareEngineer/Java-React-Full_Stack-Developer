
## Definition

`@EnableWebSocket` is a Spring WebSocket annotation used to enable native WebSocket support in a Spring application.

It tells Spring:

> Activate WebSocket infrastructure and allow registration of WebSocket handlers for real-time bidirectional communication.

Package:

```java
import org.springframework.web.socket.config.annotation.EnableWebSocket;
```

---

# What is WebSocket?

WebSocket is a protocol that provides:

```text
Full Duplex Communication
```

between:

```text
Client ↔ Server
```

over a single persistent TCP connection.

---

Unlike HTTP:

```text
Request → Response → Connection Closed
```

WebSocket:

```text
Connection Open
      ↓
Client Sends Anytime
      ↓
Server Sends Anytime
```

---

# Why Do We Need @EnableWebSocket?

Suppose you are building:

```text
Chat Application
Stock Market Dashboard
Live Cricket Scores
Trading Platform
Online Gaming
```

---

With REST APIs:

```text
Client Polls Every 2 Seconds
```

---

Problems:

```text
High Network Traffic
Slow Updates
Unnecessary Requests
```

---

With WebSocket:

```text
Server Pushes Updates Instantly
```

---

# Real-Life Analogy

HTTP:

```text
Student repeatedly asks:

"Any result?"

"Any result?"

"Any result?"
```

---

WebSocket:

```text
Teacher immediately informs:

"Result Published"
```

---

Much faster.

---

# Required Dependency

Spring Boot:

```xml
<dependency>
    <groupId>
        org.springframework.boot
    </groupId>
    <artifactId>
        spring-boot-starter-websocket
    </artifactId>
</dependency>
```

---

# Basic Configuration

```java
@Configuration
@EnableWebSocket
public class WebSocketConfig
        implements WebSocketConfigurer {

    @Override
    public void registerWebSocketHandlers(
            WebSocketHandlerRegistry registry) {

        registry.addHandler(
            new ChatHandler(),
            "/chat"
        );
    }
}
```

---

Meaning:

```text
Enable WebSocket
Register Endpoint
```

---

# What Happens Internally?

Application Startup:

```text
@EnableWebSocket
        ↓
Activate WebSocket Infrastructure
        ↓
Create Handler Mapping
        ↓
Register Endpoints
        ↓
Accept WebSocket Connections
```

---

# Architecture

```text
Browser
   │
   ▼

WebSocket Endpoint

   │
   ▼

WebSocket Handler

   │
   ▼

Business Logic
```

---

# Creating WebSocket Handler

```java
@Component
public class ChatHandler
       extends TextWebSocketHandler {

    @Override
    protected void handleTextMessage(
            WebSocketSession session,
            TextMessage message)
            throws Exception {

        System.out.println(
            message.getPayload()
        );
    }
}
```

---

# Registering Handler

```java
@Configuration
@EnableWebSocket
public class WebSocketConfig
       implements WebSocketConfigurer {

    @Override
    public void registerWebSocketHandlers(
         WebSocketHandlerRegistry registry) {

        registry.addHandler(
            new ChatHandler(),
            "/chat"
        );
    }
}
```

---

Client connects:

```text
ws://localhost:8080/chat
```

---

# Message Flow

```text
Client Connects
       ↓

WebSocket Established
       ↓

Client Sends Message
       ↓

handleTextMessage()
       ↓

Server Processes
       ↓

Server Responds
```

---

# Sending Message Back

```java
@Override
protected void handleTextMessage(
        WebSocketSession session,
        TextMessage message)
        throws Exception {

    session.sendMessage(
        new TextMessage(
            "Hello Client"
        )
    );
}
```

---

Flow:

```text
Client → Server

Server → Client
```

---

# WebSocket Session

```java
WebSocketSession session
```

represents:

```text
Connected Client
```

---

Useful methods:

```java
session.getId()

session.getPrincipal()

session.sendMessage()

session.close()
```

---

# Real Chat Application

Client A:

```text
Hello
```

---

Server:

```java
handleTextMessage(...)
```

---

Broadcast:

```text
Hello
```

to:

```text
Client B
Client C
Client D
```

---

# Lifecycle Events

## Connection Established

```java
@Override
public void afterConnectionEstablished(
        WebSocketSession session) {

}
```

---

Called when:

```text
Client Connects
```

---

## Message Received

```java
handleTextMessage(...)
```

---

Called when:

```text
Client Sends Message
```

---

## Connection Closed

```java
@Override
public void afterConnectionClosed(
    WebSocketSession session,
    CloseStatus status) {

}
```

---

Called when:

```text
Client Disconnects
```

---

# WebSocket vs HTTP

| Feature | HTTP | WebSocket |
|----------|-------|-----------|
| Connection | Short-lived | Persistent |
| Communication | One Way | Two Way |
| Real Time | No | Yes |
| Server Push | No | Yes |
| Performance | Lower | Higher |

---

# WebSocket vs REST API

REST:

```text
Client Requests
Server Responds
```

---

WebSocket:

```text
Client Sends Anytime
Server Sends Anytime
```

---

REST best for:

```text
CRUD Operations
```

---

WebSocket best for:

```text
Real-Time Communication
```

---

# WebSocket vs Kafka

| Feature | WebSocket | Kafka |
|-----------|------------|--------|
| Communication | Client-Server | Service-Service |
| Real Time | Yes | Yes |
| Persistence | No | Yes |
| Browser Support | Yes | No |
| Messaging Platform | No | Yes |

---

# Common Use Cases

## Chat Application

```text
WhatsApp
Slack
Teams
```

---

## Live Notifications

```text
New Message
New Order
New Alert
```

---

## Trading Platforms

```text
Stock Prices
Crypto Prices
```

---

## Live Cricket Scores

```text
Real-Time Updates
```

---

## Multiplayer Gaming

```text
Player Actions
```

---

# Common Mistakes

## Mistake 1

Missing Dependency

Without:

```xml
spring-boot-starter-websocket
```

WebSocket won't work.

---

# Mistake 2

Missing @EnableWebSocket

Result:

```text
No WebSocket Infrastructure
```

---

# Mistake 3

Not Registering Endpoint

```java
registry.addHandler(...)
```

missing.

---

Client cannot connect.

---

# Common Interview Questions

## What is @EnableWebSocket?

Enables WebSocket support.

---

## Why Is It Used?

To activate WebSocket infrastructure.

---

## What Does It Register?

WebSocket handlers and endpoints.

---

## Difference Between HTTP and WebSocket?

Request-response vs persistent bidirectional communication.

---

## When Should We Use WebSocket?

Real-time applications.

---

# Enterprise Best Practice

Use:

```java
@EnableWebSocket
```

for:

```text
Native WebSocket Applications
```

---

For enterprise applications prefer:

```text
STOMP
SockJS
@EnableWebSocketMessageBroker
```

because they provide:

```text
Topics
Subscriptions
Message Routing
Broadcasting
```

---

# Key Points To Remember

- `@EnableWebSocket` enables native WebSocket support.
- Activates WebSocket infrastructure.
- Allows registration of WebSocket endpoints.
- Supports bidirectional communication.
- Provides real-time updates.
- Used in chat, gaming, trading, and notification systems.
- Works with `WebSocketHandler`.
- Creates persistent connections.
- Different from REST APIs.
- Frequently asked Spring interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EnableWebSocket` | Enable Native WebSocket |
| `@EnableWebSocketMessageBroker` | Enable STOMP Messaging |
| `@MessageMapping` | Handle Messages |
| `@SendTo` | Send Messages To Subscribers |

---

# Interview Shortcut

```java
@Configuration
@EnableWebSocket
public class WebSocketConfig
       implements WebSocketConfigurer {

}
```

Meaning:

```text
Activate WebSocket

Register Endpoints

Allow Real-Time Communication

Enable Bidirectional Messaging
```

---

# Real Production Scenario

```text
Trading Dashboard
```

Flow:

```text
Stock Price Changes
         ↓
Server Receives Event
         ↓
WebSocket Pushes Update
         ↓
Browser Updates Instantly
```

Without polling.

---

Result:

```text
Low Latency
Real-Time Updates
Efficient Communication
Better User Experience
```

`@EnableWebSocket` is a Spring WebSocket annotation that activates native WebSocket infrastructure, allowing applications to register WebSocket endpoints and support real-time, bidirectional communication between clients and servers.