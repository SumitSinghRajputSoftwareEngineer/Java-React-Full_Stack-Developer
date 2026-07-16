
## Definition

`@SendToUser` is a Spring WebSocket/STOMP annotation used to send a message **only to the currently authenticated user** instead of broadcasting it to all subscribers.

It tells Spring:

> Send the method's return value to a user-specific destination.

Package:

```java
import org.springframework.messaging.simp.annotation.SendToUser;
```

---

# Why Do We Need @SendToUser?

Suppose:

```text
1000 Users Connected
```

---

User A requests:

```text
My Profile Details
```

---

Should Spring send the response to:

```text
All 1000 Users?
```

No.

---

Only:

```text
User A
```

should receive it.

---

This is where:

```java
@SendToUser
```

is used.

---

# Real-Life Analogy

Imagine:

```text
Bank Counter
```

---

Customer asks:

```text
What is my account balance?
```

---

Bank employee tells:

```text
Only That Customer
```

---

Not the entire bank.

---

Similarly:

```java
@SendToUser
```

sends messages only to the requesting user.

---

# @SendTo vs @SendToUser

Interview Favorite.

---

## @SendTo

Broadcasts.

```java
@SendTo("/topic/chat")
```

---

All subscribers receive.

---

Example:

```text
Group Chat
Stock Updates
Announcements
```

---

## @SendToUser

Private messaging.

```java
@SendToUser("/queue/reply")
```

---

Only current user receives.

---

Comparison:

| Annotation | Receiver |
|------------|-----------|
| @SendTo | All Subscribers |
| @SendToUser | Current User Only |

---

# Basic Example

```java
@Controller
public class UserController {

    @MessageMapping("/profile")

    @SendToUser("/queue/profile")
    public String profile() {

        return "Private Data";
    }
}
```

---

Client sends:

```text
/app/profile
```

---

Response goes to:

```text
/queue/profile
```

for that user only.

---

# What Happens Internally?

User:

```text
John
```

sends:

```text
/app/profile
```

---

Spring:

```text
Execute Method
      ↓
Return Value
      ↓
Resolve User Session
      ↓
Send Only To John
```

---

Other users:

```text
Do Not Receive
```

---

# Architecture

```text
John
  │
 SEND
  │
  ▼

/app/profile

  │
  ▼

@MessageMapping

  │
  ▼

@SendToUser

  │
  ▼

/user/queue/profile

  │
  ▼

John Only
```

---

# Basic Flow

Controller:

```java
@MessageMapping("/hello")

@SendToUser("/queue/reply")
public String hello() {

    return "Hello User";
}
```

---

Client:

```text
/app/hello
```

---

Response:

```text
/queue/reply
```

---

Only requester receives it.

---

# User Destination Prefix

Spring automatically uses:

```text
/user
```

prefix.

---

Configuration:

```java
registry.setUserDestinationPrefix(
      "/user"
);
```

---

Default:

```text
/user
```

---

Actual subscription:

```text
/user/queue/reply
```

---

# Client Side Example

Subscribe:

```javascript
stompClient.subscribe(
    "/user/queue/reply",
    callback
);
```

---

Send:

```javascript
stompClient.send(
    "/app/hello",
    {},
    ""
);
```

---

Response:

```text
Only Current User Receives
```

---

# DTO Example

DTO:

```java
public class Profile {

    private String name;
    private String email;
}
```

---

Controller:

```java
@MessageMapping("/profile")

@SendToUser("/queue/profile")
public Profile getProfile() {

    return service.getProfile();
}
```

---

Spring converts:

```text
Profile
   ↓
JSON
```

---

User receives:

```json
{
  "name":"Sumit",
  "email":"abc@gmail.com"
}
```

---

# Accessing Authenticated User

```java
@MessageMapping("/profile")
@SendToUser("/queue/profile")
public UserProfile getProfile(
        Principal principal) {

    return service.getProfile(
        principal.getName()
    );
}
```

---

Useful for:

```text
User Specific Data
```

---

# Error Handling Example

```java
@MessageExceptionHandler

@SendToUser("/queue/errors")
public String handleError(
      Exception ex) {

    return ex.getMessage();
}
```

---

User receives:

```text
Error Message
```

---

Only that user.

---

# Real Production Scenario

Banking App:

User requests:

```text
Account Balance
```

---

Controller:

```java
@MessageMapping("/balance")

@SendToUser("/queue/balance")
public Balance getBalance() {

}
```

---

Response:

```text
Balance Details
```

sent only to:

```text
That User
```

---

# Private Chat Example

```java
@MessageMapping("/private")

@SendToUser("/queue/messages")
public String privateChat(
       String msg) {

    return msg;
}
```

---

Useful for:

```text
Private Conversations
```

---

# Notification Example

```java
@MessageMapping("/notifications")

@SendToUser("/queue/alerts")
public Notification notifyUser() {

}
```

---

Only targeted user receives:

```text
Alert
```

---

# @SendToUser vs SimpMessagingTemplate

## @SendToUser

Simple.

```java
@SendToUser("/queue/reply")
```

---

Automatically sends return value.

---

## SimpMessagingTemplate

More control.

```java
messagingTemplate
    .convertAndSendToUser(
         username,
         "/queue/reply",
         data
    );
```

---

Useful when:

```text
Sending To Another User
Custom Routing
```

---

# Example Using SimpMessagingTemplate

```java
@Autowired
private SimpMessagingTemplate
        messagingTemplate;
```

---

```java
messagingTemplate
   .convertAndSendToUser(
       "john",
       "/queue/reply",
       "Hello"
   );
```

---

Sends directly to:

```text
John
```

---

# Common Use Cases

## User Notifications

```text
Password Expiry
```

---

## Banking

```text
Balance Updates
```

---

## E-Commerce

```text
Order Status
```

---

## Private Chat

```text
One-To-One Messaging
```

---

## Error Responses

```text
Validation Errors
```

---

# Common Mistakes

## Mistake 1

Client subscribes:

```text
/queue/reply
```

instead of:

```text
/user/queue/reply
```

---

Messages not received.

---

# Mistake 2

No Authentication

Spring cannot identify user.

---

# Mistake 3

Using @SendToUser For Broadcast

Wrong choice.

Use:

```java
@SendTo
```

instead.

---

# Common Interview Questions

## What is @SendToUser?

Sends message only to current user.

---

## Difference Between @SendTo and @SendToUser?

Broadcast vs private messaging.

---

## What Prefix Is Used?

```text
/user
```

---

## Can It Return DTO Objects?

Yes.

Automatically serialized.

---

## Is Authentication Required?

Typically yes.

To identify the user.

---

# Enterprise Best Practice

Use:

```java
@SendToUser
```

for:

```text
Private Notifications
Personal Data
Error Messages
```

---

Use:

```java
SimpMessagingTemplate
```

for:

```text
Sending To Arbitrary Users
```

---

Always secure with:

```text
Spring Security
JWT
OAuth2
```

---

# Key Points To Remember

- `@SendToUser` sends messages to one user only.
- Works with `@MessageMapping`.
- Uses `/user` destination prefix.
- Ideal for private notifications.
- Ideal for personal data responses.
- Supports DTO objects.
- Supports authenticated users.
- Not used for broadcasting.
- Alternative: `SimpMessagingTemplate.convertAndSendToUser()`.
- Frequently asked Spring WebSocket interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@MessageMapping` | Receive Messages |
| `@SendTo` | Broadcast To All |
| `@SendToUser` | Send To Current User |
| `SimpMessagingTemplate` | Manual Messaging |
| `@EnableWebSocketMessageBroker` | Enable STOMP |

---

# Interview Shortcut

```java
@MessageMapping("/profile")

@SendToUser("/queue/profile")
public Profile profile() {

    return profileService.get();
}
```

Meaning:

```text
Receive Request

Process Request

Send Response

Only To Requesting User
```

---

# Real Production Example

```java
@Controller
public class NotificationController {

    @MessageMapping("/notifications")

    @SendToUser("/queue/alerts")
    public Notification getNotifications() {

        return service.getAlerts();
    }
}
```

Flow:

```text
User Requests Alerts
          ↓
/app/notifications
          ↓
@MessageMapping
          ↓
Business Logic
          ↓
@SendToUser
          ↓
/user/queue/alerts
          ↓
Only That User Receives
```

Result:

```text
Private Communication
Secure Messaging
Personal Notifications
Real-Time Updates
```

`@SendToUser` is a Spring WebSocket/STOMP annotation that sends a controller method's return value to a user-specific destination, ensuring that only the current authenticated user receives the message rather than all subscribers.