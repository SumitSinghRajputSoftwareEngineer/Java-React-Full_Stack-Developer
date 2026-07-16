
## Definition

`@SendTo` is a Spring WebSocket/STOMP annotation used to send the return value of a method to a specific destination (topic or queue).

It tells Spring:

> Take the value returned by this method and publish it to the specified destination so that subscribed clients can receive it.

Package:

```java
import org.springframework.messaging.handler.annotation.SendTo;
```

---

# Simple Understanding

Think of:

```java
@MessageMapping
```

as:

```text
Receiving Messages
```

---

and

```java
@SendTo
```

as:

```text
Sending/Broadcasting Messages
```

---

Together:

```java
Client Sends Message
        ↓
@MessageMapping
        ↓
Business Logic
        ↓
@SendTo
        ↓
Subscribers Receive
```

---

# Why Do We Need @SendTo?

Suppose:

```text
Chat Application
```

User A sends:

```text
Hello Everyone
```

---

Server receives message.

---

How will:

```text
User B
User C
User D
```

receive it?

---

Using:

```java
@SendTo("/topic/messages")
```

---

Spring automatically broadcasts the response.

---

# Real-Life Analogy

Imagine a classroom.

---

Student asks:

```text
What is Java?
```

---

Teacher answers:

```text
Java is a programming language.
```

---

Everyone hears the answer.

---

`@SendTo` works similarly:

```text
One Message In
Many Users Receive
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

# Basic Example

```java
@Controller
public class ChatController {

    @MessageMapping("/chat")

    @SendTo("/topic/messages")
    public String send(
            String msg) {

        return msg;
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
All Subscribers Receive Message
```

---

# What Happens Internally?

Client:

```text
SEND /app/chat
```

---

Spring:

```text
Find @MessageMapping
         ↓
Execute Method
         ↓
Get Return Value
         ↓
Publish To @SendTo Destination
```

---

# Architecture

```text
Client A
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

@SendTo("/topic/messages")

    │
    ▼

Message Broker

    │
    ▼

Subscribers
```

---

# Chat Example

Controller:

```java
@Controller
public class ChatController {

    @MessageMapping("/chat")

    @SendTo("/topic/chat")
    public String process(
            String msg) {

        return msg;
    }
}
```

---

Client A sends:

```text
Hello
```

---

Subscribers:

```text
User B

User C

User D
```

receive:

```text
Hello
```

---

# Returning DTO Objects

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
@SendTo("/topic/chat")
public ChatMessage send(
       ChatMessage message) {

    return message;
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

Outgoing JSON:

```json
{
   "sender":"Sumit",
   "content":"Hello"
}
```

---

Spring automatically converts:

```text
Java Object
      ↓
JSON
```

---

# Broadcasting Example

```java
@MessageMapping("/stock")
@SendTo("/topic/stocks")
public StockPrice update(
       StockPrice price) {

    return price;
}
```

---

All subscribers:

```text
/topic/stocks
```

receive:

```text
Updated Stock Price
```

---

# Real-Time Notification Example

```java
@MessageMapping("/order")
@SendTo("/topic/orders")
public Order process(
        Order order) {

    return order;
}
```

---

Useful for:

```text
Order Tracking
```

---

Flow:

```text
Order Created
      ↓
Broker
      ↓
All Clients Updated
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

Return Object

   │
   ▼

@SendTo

   │
   ▼

/topic/chat

   │
   ▼

Subscribers
```

---

# @SendTo vs @SendToUser

Interview Favorite.

---

## @SendTo

Broadcasts to:

```text
All Subscribers
```

---

Example:

```java
@SendTo("/topic/chat")
```

---

## @SendToUser

Sends only to:

```text
Current User
```

---

Example:

```java
@SendToUser("/queue/reply")
```

---

Comparison:

| Annotation | Sends To |
|------------|-----------|
| @SendTo | Everyone |
| @SendToUser | Single User |

---

# @SendTo vs SimpMessagingTemplate

## @SendTo

Simple broadcasting.

```java
@SendTo("/topic/chat")
```

---

Automatically sends method return value.

---

## SimpMessagingTemplate

Manual sending.

```java
messagingTemplate
    .convertAndSend(
        "/topic/chat",
        message
    );
```

---

More flexible.

---

# Example Using SimpMessagingTemplate

```java
@Autowired
private SimpMessagingTemplate
        messagingTemplate;
```

---

```java
@MessageMapping("/chat")
public void process(
        String msg) {

    messagingTemplate
      .convertAndSend(
          "/topic/chat",
          msg
      );
}
```

---

Useful for:

```text
Conditional Routing
Multiple Destinations
Complex Logic
```

---

# Common Destinations

## Topic

```java
@SendTo("/topic/chat")
```

---

Broadcast.

---

## Queue

```java
@SendTo("/queue/updates")
```

---

Point-to-point messaging.

---

# Real Production Scenario

Chat Application:

```text
User A
```

sends:

```text
Hello Team
```

---

Controller:

```java
@MessageMapping("/chat")
```

---

Broadcast:

```java
@SendTo("/topic/messages")
```

---

Users:

```text
B

C

D
```

receive:

```text
Hello Team
```

---

# Common Use Cases

## Chat Applications

```text
Group Chat
```

---

## Notifications

```text
Alerts
Announcements
```

---

## Stock Market

```text
Price Updates
```

---

## Live Scores

```text
Cricket
Football
```

---

## Multiplayer Gaming

```text
Player Events
```

---

# Common Mistakes

## Mistake 1

No Subscriber

Broadcast succeeds.

---

Nobody receives.

---

# Mistake 2

Wrong Destination

```java
@SendTo("/topic/chat")
```

---

Client subscribes:

```text
/topic/messages
```

---

No messages received.

---

# Mistake 3

Missing Broker

```java
enableSimpleBroker()
```

not configured.

---

Subscriptions fail.

---

# Common Interview Questions

## What is @SendTo?

Publishes method return value to a destination.

---

## Does It Send To All Users?

Yes, all subscribers.

---

## Difference Between @SendTo and @SendToUser?

Broadcast vs single user.

---

## Can It Return DTO Objects?

Yes.

Automatically serialized to JSON.

---

## Is It Mandatory?

No.

Can use:

```java
SimpMessagingTemplate
```

instead.

---

# Enterprise Best Practice

Use:

```java
@SendTo
```

for:

```text
Simple Broadcasting
```

---

Use:

```java
SimpMessagingTemplate
```

for:

```text
Complex Routing
Multiple Topics
Conditional Sending
```

---

Secure destinations using:

```text
JWT
Spring Security
OAuth2
```

---

# Key Points To Remember

- `@SendTo` publishes method return values.
- Works with `@MessageMapping`.
- Sends messages to topics or queues.
- Used for broadcasting.
- Supports DTO objects.
- Automatically serializes responses.
- Works with STOMP message broker.
- Ideal for chat and notification systems.
- Simpler than `SimpMessagingTemplate`.
- Frequently asked Spring interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@MessageMapping` | Receive Messages |
| `@SendTo` | Broadcast Messages |
| `@SendToUser` | Send To Current User |
| `@EnableWebSocketMessageBroker` | Enable STOMP |
| `SimpMessagingTemplate` | Manual Messaging |

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

To All Subscribers
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
Business Logic
        ↓
@SendTo
        ↓
/topic/messages
        ↓
All Connected Users Receive
```

Result:

```text
Real-Time Broadcasting
Instant Updates
Group Messaging
Scalable Communication
```

`@SendTo` is a Spring WebSocket/STOMP annotation that publishes the return value of a controller method to a specified topic or queue, allowing subscribed clients to receive messages in real time.