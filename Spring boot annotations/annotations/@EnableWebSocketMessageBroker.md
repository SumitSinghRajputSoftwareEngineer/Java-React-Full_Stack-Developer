
## Definition

`@EnableWebSocketMessageBroker` is a Spring annotation that enables a **STOMP-based WebSocket messaging system**.

It tells Spring:

> Enable WebSocket message handling, message routing, topic subscriptions, and message broker support.

Package:

```java
import org.springframework.web.socket.config.annotation.EnableWebSocketMessageBroker;
```

---

# What Problem Does It Solve?

Imagine building:

```text
WhatsApp
Slack
Microsoft Teams
Trading Dashboard
Live Notifications
```

---

With plain WebSocket (`@EnableWebSocket`):

```text
You Handle Connections
You Handle Sessions
You Handle Routing
You Handle Broadcasting
```

Everything is manual.

---

Spring introduced:

```java
@EnableWebSocketMessageBroker
```

to provide:

```text
Message Routing
Topics
Subscriptions
Broadcasting
Pub/Sub Model
```

automatically.

---

# Real-Life Analogy

Think of:

```text
TV Broadcasting System
```

---

TV Channel:

```text
Sports
News
Movies
```

---

Users subscribe:

```text
Sports Lovers → Sports Channel

News Lovers → News Channel
```

---

When a sports event occurs:

```text
Only Sports Subscribers Receive It
```

---

Exactly how:

```text
Topics
Subscriptions
```

work in STOMP WebSockets.

---

# Native WebSocket vs Message Broker

## Native WebSocket

```java
@EnableWebSocket
```

You manually manage:

```text
Sessions
Connections
Broadcasting
```

---

## Message Broker

```java
@EnableWebSocketMessageBroker
```

Spring manages:

```text
Topics
Subscriptions
Routing
Messaging
```

---

# Required Dependency

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
@EnableWebSocketMessageBroker
public class WebSocketConfig
        implements WebSocketMessageBrokerConfigurer {

}
```

---

Meaning:

```text
Enable STOMP Messaging
Enable Message Broker
```

---

# Complete Configuration

```java
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig
      implements
      WebSocketMessageBrokerConfigurer {

    @Override
    public void registerStompEndpoints(
          StompEndpointRegistry registry) {

        registry.addEndpoint("/chat");
    }

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

# What Happens Internally?

Application Startup:

```text
@EnableWebSocketMessageBroker
             ↓
Create STOMP Infrastructure
             ↓
Register Endpoints
             ↓
Create Message Broker
             ↓
Handle Subscriptions
             ↓
Route Messages
```

---

# Architecture

```text
Client
   │
   ▼

/chat Endpoint

   │
   ▼

Spring STOMP

   │
   ▼

Message Broker

   │
   ▼

Subscribers
```

---

# Important Components

## Endpoint

```java
registry.addEndpoint("/chat");
```

Client connects:

```text
ws://localhost:8080/chat
```

---

# Message Broker

```java
registry.enableSimpleBroker(
    "/topic"
);
```

Creates:

```text
In-Memory Broker
```

---

Responsible for:

```text
Subscriptions
Broadcasting
```

---

# Application Prefix

```java
registry.setApplicationDestinationPrefixes(
    "/app"
);
```

---

Client sends:

```text
/app/message
```

---

Spring routes to:

```java
@MessageMapping("/message")
```

---

# Message Flow

```text
Client Sends
      │
      ▼

/app/message

      │
      ▼

@MessageMapping

      │
      ▼

Broker

      │
      ▼

/topic/messages

      │
      ▼

Subscribers
```

---

# Creating Controller

```java
@Controller
public class ChatController {

    @MessageMapping("/message")

    @SendTo("/topic/messages")
    public String send(
            String msg) {

        return msg;
    }
}
```

---

Meaning:

```text
Receive Message
      ↓
Process Message
      ↓
Broadcast To Topic
```

---

# Client Subscription

Client subscribes:

```text
/topic/messages
```

---

Whenever:

```java
@SendTo("/topic/messages")
```

publishes,

all subscribers receive it.

---

# Broadcasting Example

Client A sends:

```text
Hello Everyone
```

---

Controller:

```java
@MessageMapping("/message")
```

---

Broker:

```text
/topic/messages
```

---

Clients:

```text
B receives

C receives

D receives
```

---

# STOMP Protocol

STOMP means:

```text
Simple Text Oriented
Messaging Protocol
```

---

Provides:

```text
CONNECT

SEND

SUBSCRIBE

DISCONNECT
```

commands.

---

Without STOMP:

```text
Raw WebSocket Messages
```

---

With STOMP:

```text
Structured Messaging
```

---

# Private Messaging

```java
@SendToUser("/queue/private")
```

---

Used for:

```text
Personal Notifications
Private Chat
```

---

Example:

```java
@MessageMapping("/private")
@SendToUser("/queue/reply")
public String reply() {

    return "Private Message";
}
```

---

# Real Chat Application

Configuration:

```java
@EnableWebSocketMessageBroker
```

---

Client connects:

```text
/chat
```

---

Client sends:

```text
/app/message
```

---

Controller:

```java
@MessageMapping("/message")
```

---

Broker broadcasts:

```text
/topic/messages
```

---

All users receive:

```text
Chat Message
```

---

# Simple Broker vs External Broker

## Simple Broker

```java
registry.enableSimpleBroker(
    "/topic"
);
```

---

Characteristics:

```text
In-Memory
Lightweight
Easy Setup
```

---

Good for:

```text
Small Applications
```

---

## External Broker

```text
RabbitMQ
ActiveMQ
Kafka (via integrations)
```

---

Characteristics:

```text
Distributed
Scalable
Persistent
```

---

Good for:

```text
Enterprise Systems
```

---

# Common Use Cases

## Chat Applications

```text
WhatsApp
Slack
Teams
```

---

## Trading Dashboards

```text
Stock Updates
```

---

## Notifications

```text
Real-Time Alerts
```

---

## Multiplayer Games

```text
Player Updates
```

---

## Live Sports

```text
Cricket Scores
Football Scores
```

---

# @EnableWebSocket vs @EnableWebSocketMessageBroker

| Feature | EnableWebSocket | EnableWebSocketMessageBroker |
|----------|----------------|-----------------------------|
| Native WebSocket | Yes | Yes |
| STOMP | No | Yes |
| Topics | No | Yes |
| Subscriptions | No | Yes |
| Message Routing | No | Yes |
| Broadcasting | Manual | Automatic |
| Enterprise Friendly | Limited | Excellent |

---

# Common Mistakes

## Mistake 1

Missing Endpoint

```java
registerStompEndpoints()
```

not configured.

---

Client cannot connect.

---

# Mistake 2

Wrong Prefix

Configured:

```java
/app
```

---

Client sends:

```text
/message
```

---

Controller not reached.

---

# Mistake 3

Missing Broker

```java
enableSimpleBroker()
```

not configured.

---

Subscriptions won't work.

---

# Common Interview Questions

## What is @EnableWebSocketMessageBroker?

Enables STOMP-based WebSocket messaging.

---

## Difference From @EnableWebSocket?

Adds:

```text
Broker
Topics
Subscriptions
Routing
```

---

## What is STOMP?

Simple Text Oriented Messaging Protocol.

---

## What is /app?

Messages sent to controllers.

---

## What is /topic?

Messages sent to subscribers.

---

## What Does Message Broker Do?

Routes and broadcasts messages.

---

# Enterprise Best Practice

Use:

```java
@EnableWebSocketMessageBroker
```

instead of:

```java
@EnableWebSocket
```

for most real-world systems.

---

Use:

```text
RabbitMQ
ActiveMQ
```

as external brokers for scalability.

---

Secure endpoints using:

```text
Spring Security
JWT
OAuth2
```

---

# Key Points To Remember

- `@EnableWebSocketMessageBroker` enables STOMP messaging over WebSockets.
- Supports publish-subscribe architecture.
- Enables topics and subscriptions.
- Provides automatic message routing.
- Works with `@MessageMapping`.
- Works with `@SendTo`.
- Uses a message broker.
- Ideal for chat and real-time systems.
- More powerful than native WebSocket support.
- Frequently asked Spring interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EnableWebSocket` | Native WebSocket |
| `@EnableWebSocketMessageBroker` | STOMP Messaging |
| `@MessageMapping` | Handle Messages |
| `@SendTo` | Broadcast Messages |
| `@SendToUser` | Private Messages |

---

# Interview Shortcut

```java
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig {
}
```

Meaning:

```text
Enable WebSockets

Enable STOMP

Enable Topics

Enable Subscriptions

Enable Message Routing
```

---

# Real Production Example

```text
Chat Application
```

Flow:

```text
User Sends Message
        ↓
/app/chat
        ↓
@MessageMapping
        ↓
Broker
        ↓
/topic/messages
        ↓
All Users Receive Message
```

Result:

```text
Real-Time Communication
Broadcast Messaging
Scalable Architecture
Low Latency
```

`@EnableWebSocketMessageBroker` is a Spring annotation that enables STOMP-based WebSocket messaging, providing message routing, topic subscriptions, broadcasting, and broker support for building real-time applications such as chat systems, live dashboards, and notification platforms.