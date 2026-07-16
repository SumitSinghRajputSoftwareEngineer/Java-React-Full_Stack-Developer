
## Definition

`@MessageMapping` is a Spring WebSocket/STOMP annotation used to map incoming WebSocket messages to a controller method, similar to how `@RequestMapping` maps HTTP requests.

It tells Spring:

> When a client sends a message to a specific STOMP destination, invoke this method.

Package:

```java
import org.springframework.messaging.handler.annotation.MessageMapping;
```

---

# Simple Understanding

Think of:

```java
@GetMapping("/users")
```

for REST APIs.

---

Similarly:

```java
@MessageMapping("/chat")
```

for WebSockets.

---

Comparison:

| REST | WebSocket |
|--------|------------|
| @GetMapping | @MessageMapping |
| HTTP Request | STOMP Message |
| Request/Response | Real-Time Messaging |

---

# Why Do We Need @MessageMapping?

Suppose a chat application.

Client sends:

```text
Hello Everyone
```

---

Without `@MessageMapping`:

```text
Spring Doesn't Know
Where To Route Message
```

---

With:

```java
@MessageMapping("/chat")
```

Spring routes incoming messages to the correct method.

---

# Real-Life Analogy

Imagine a post office.

Letter destination:

```text
Billing Department
```

---

Post office routes letter to:

```text
Billing Team
```

---

Similarly:

```text
/app/chat
```

is routed to:

```java
@MessageMapping("/chat")
```

---

# Prerequisite Configuration

```java
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig
        implements WebSocketMessageBrokerConfigurer {

    @Override
    public void configureMessageBroker(
        MessageBrokerRegistry registry) {

        registry.enableSimpleBroker(
            "/topic"
        );

        registry.setApplicationDestinationPrefixes(
            "/app"
        );
    }
}
```

---

Application Prefix:

```text
/app
```

means:

```java
@MessageMapping("/chat")
```

is accessible through:

```text
/app/chat
```

---

# Basic Example

Controller:

```java
@Controller
public class ChatController {

    @MessageMapping("/chat")
    public void receive(
            String message) {

        System.out.println(message);
    }
}
```

---

Client sends:

```text
/app/chat
```

---

Result:

```java
receive()
```

gets executed.

---

# What Happens Internally?

Client:

```text
SEND /app/chat
```

---

Spring:

```text
Check Destination
       ↓
Find Matching
@MessageMapping
       ↓
Invoke Method
```

---

# Architecture

```text
Client
   │
   ▼

/app/chat

   │
   ▼

@MessageMapping("/chat")

   │
   ▼

Business Logic
```

---

# Returning Data

```java
@MessageMapping("/chat")
@SendTo("/topic/messages")
public String process(
        String msg) {

    return msg;
}
```

---

Flow:

```text
Client Sends
      ↓

/app/chat

      ↓

@MessageMapping

      ↓

@SendTo

      ↓

/topic/messages

      ↓

Subscribers Receive
```

---

# Chat Application Example

Controller:

```java
@Controller
public class ChatController {

    @MessageMapping("/chat")
    @SendTo("/topic/messages")
    public String send(
            String message) {

        return message;
    }
}
```

---

Client sends:

```text
/app/chat
```

---

Subscribers listen:

```text
/topic/messages
```

---

Result:

```text
Broadcast Message
```

---

# Receiving DTO Objects

DTO:

```java
public class ChatMessage {

    private String sender;
    private String content;
}
```

---

Controller:

```java
@MessageMapping("/chat")
public void process(
        ChatMessage message) {

    System.out.println(
        message.getContent()
    );
}
```

---

Incoming JSON:

```json
{
  "sender":"Sumit",
  "content":"Hello"
}
```

---

Spring converts:

```text
JSON
 ↓
ChatMessage
```

automatically.

---

# Dynamic Path Variables

Like REST:

```java
@GetMapping("/chat/{id}")
```

---

WebSocket:

```java
@MessageMapping("/chat/{roomId}")
public void process(
    @DestinationVariable
    String roomId) {

}
```

---

Client sends:

```text
/app/chat/101
```

---

Result:

```java
roomId = "101"
```

---

# Example

```java
@MessageMapping(
    "/chat/{roomId}"
)
public void process(
    @DestinationVariable
    String roomId,

    ChatMessage msg) {

}
```

---

Useful for:

```text
Chat Rooms
Game Rooms
Live Sessions
```

---

# Accessing User Information

```java
@MessageMapping("/chat")
public void process(
      Principal principal) {

    System.out.println(
        principal.getName()
    );
}
```

---

Useful for:

```text
Authenticated Users
Private Messaging
```

---

# Private Messaging Example

```java
@MessageMapping("/private")
@SendToUser("/queue/reply")
public String privateMessage(
        String msg) {

    return msg;
}
```

---

Only sender receives:

```text
/queue/reply
```

---

# Using SimpMessagingTemplate

Instead of:

```java
@SendTo
```

---

Use:

```java
@Autowired
private SimpMessagingTemplate
        messagingTemplate;
```

---

Example:

```java
@MessageMapping("/chat")
public void process(
        String msg) {

    messagingTemplate
       .convertAndSend(
            "/topic/messages",
            msg
       );
}
```

---

Useful for:

```text
Custom Routing
Conditional Broadcasting
```

---

# Message Flow

```text
Client
   │
 SEND
   │
   ▼

/app/chat

   │
   ▼

@MessageMapping

   │
   ▼

Business Logic

   │
   ▼

@SendTo

   │
   ▼

/topic/messages

   │
   ▼

Subscribers
```

---

# @MessageMapping vs @RequestMapping

| Feature | RequestMapping | MessageMapping |
|----------|---------------|----------------|
| Protocol | HTTP | WebSocket |
| Triggered By | HTTP Request | STOMP Message |
| Real Time | No | Yes |
| Persistent Connection | No | Yes |

---

# @MessageMapping vs @SendTo

Interview Favorite.

---

## @MessageMapping

Receives messages.

```java
@MessageMapping("/chat")
```

---

## @SendTo

Sends messages.

```java
@SendTo("/topic/chat")
```

---

Comparison:

| Annotation | Purpose |
|------------|----------|
| @MessageMapping | Receive |
| @SendTo | Broadcast |

---

# Common Use Cases

## Chat Applications

```text
WhatsApp
Slack
Teams
```

---

## Live Notifications

```text
Order Updates
Alerts
```

---

## Stock Market

```text
Price Updates
```

---

## Multiplayer Gaming

```text
Player Events
```

---

## Collaborative Editing

```text
Google Docs Style Apps
```

---

# Common Mistakes

## Mistake 1

Wrong Prefix

Configured:

```java
/app
```

---

Client sends:

```text
/chat
```

instead of:

```text
/app/chat
```

---

Controller not called.

---

# Mistake 2

Missing @EnableWebSocketMessageBroker

Result:

```text
No STOMP Infrastructure
```

---

# Mistake 3

Wrong Destination

```java
@MessageMapping("/chat")
```

but sending:

```text
/app/message
```

---

No match.

---

# Common Interview Questions

## What is @MessageMapping?

Maps WebSocket/STOMP messages to methods.

---

## Is It Similar To @RequestMapping?

Yes.

For WebSockets instead of HTTP.

---

## What Prefix Is Usually Used?

```text
/app
```

---

## Can It Accept DTOs?

Yes.

JSON automatically converts to Java objects.

---

## Can It Use Path Variables?

Yes.

Using:

```java
@DestinationVariable
```

---

# Enterprise Best Practice

Use:

```java
@MessageMapping
```

for incoming client messages.

---

Use:

```java
@SendTo
```

for simple broadcasting.

---

Use:

```java
SimpMessagingTemplate
```

for advanced routing.

---

Secure endpoints using:

```text
JWT
OAuth2
Spring Security
```

---

# Key Points To Remember

- `@MessageMapping` handles incoming WebSocket/STOMP messages.
- Similar to `@RequestMapping` for HTTP.
- Works with `@EnableWebSocketMessageBroker`.
- Usually receives messages sent to `/app/*`.
- Supports DTO binding.
- Supports path variables.
- Supports authenticated users.
- Often combined with `@SendTo`.
- Used in real-time applications.
- Frequently asked Spring interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@MessageMapping` | Receive Messages |
| `@SendTo` | Broadcast Messages |
| `@SendToUser` | Private Messages |
| `@EnableWebSocketMessageBroker` | Enable STOMP |
| `@DestinationVariable` | Path Variables |

---

# Interview Shortcut

```java
@MessageMapping("/chat")
@SendTo("/topic/messages")
public String send(
        String msg) {

    return msg;
}
```

Meaning:

```text
Receive Message

Process Message

Broadcast Result

To Subscribers
```

---

# Real Production Example

```java
@Controller
public class ChatController {

    @MessageMapping("/chat")
    @SendTo("/topic/messages")
    public ChatMessage send(
           ChatMessage message) {

        return message;
    }
}
```

Flow:

```text
User Sends Message
       ↓
/app/chat
       ↓
@MessageMapping
       ↓
Process Message
       ↓
/topic/messages
       ↓
All Connected Users Receive
```

Result:

```text
Real-Time Chat
Instant Updates
Bidirectional Communication
Scalable Messaging
```

`@MessageMapping` is a Spring WebSocket/STOMP annotation that maps incoming messages from WebSocket clients to controller methods, enabling real-time processing of client messages in applications such as chat systems, live dashboards, notifications, and multiplayer games.