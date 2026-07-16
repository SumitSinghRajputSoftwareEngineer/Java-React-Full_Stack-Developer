
## Definition

`@EventListener` is a Spring annotation used to listen for and handle **application events**.

It tells Spring:

> When a specific event occurs, automatically invoke this method.

Package:

```java
import org.springframework.context.event.EventListener;
```

---

# What Is an Event?

An event represents:

```text
Something Happened
```

Examples:

```text
User Registered
Order Placed
Payment Completed
Email Sent
Application Started
```

---

Instead of directly calling another service:

```java
userService.register();

emailService.sendWelcomeEmail();
```

we can publish an event:

```java
UserRegisteredEvent
```

and let interested components react.

---

# Why Do We Need Events?

Without events:

```java
public void registerUser() {

    saveUser();

    sendEmail();

    createAuditLog();

    createWelcomeCoupon();

    notifyCRM();
}
```

---

Problem:

```text
Tightly Coupled Code
Hard To Maintain
Hard To Extend
```

---

Solution:

```text
Publish Event
```

---

Flow:

```text
User Registered
      ↓
Publish Event
      ↓
Email Service
      ↓
Audit Service
      ↓
Notification Service
```

---

# Basic Example

## Event Class

```java
public class UserRegisteredEvent {

    private String email;

    public UserRegisteredEvent(
            String email) {

        this.email = email;
    }

    public String getEmail() {

        return email;
    }
}
```

---

## Publish Event

```java
@Service
public class UserService {

    @Autowired
    private ApplicationEventPublisher
            publisher;

    public void registerUser(
            String email) {

        publisher.publishEvent(
            new UserRegisteredEvent(
                email
            )
        );
    }
}
```

---

## Listen Event

```java
@Component
public class EmailListener {

    @EventListener
    public void handleUserRegistered(
            UserRegisteredEvent event) {

        System.out.println(
           "Sending email to "
           + event.getEmail()
        );
    }
}
```

---

Execution:

```text
Register User
      ↓
Publish Event
      ↓
Listener Invoked
      ↓
Send Email
```

---

# What Happens Internally?

```text
Event Published
      ↓
ApplicationEventPublisher
      ↓
Spring Event Dispatcher
      ↓
Matching Listener Found
      ↓
Method Executed
```

---

# Internal Workflow

```text
publishEvent()
      ↓
Application Context
      ↓
Event Multicaster
      ↓
@EventListener
      ↓
Execute Listener Method
```

---

# Multiple Listeners

One event can trigger multiple listeners.

---

Event:

```java
UserRegisteredEvent
```

---

Listener 1:

```java
@EventListener
public void sendEmail(
        UserRegisteredEvent event) {

}
```

---

Listener 2:

```java
@EventListener
public void createAudit(
        UserRegisteredEvent event) {

}
```

---

Listener 3:

```java
@EventListener
public void generateCoupon(
        UserRegisteredEvent event) {

}
```

---

Execution:

```text
User Registered
      ↓
Email Sent
      ↓
Audit Created
      ↓
Coupon Generated
```

---

# Listening To Built-In Spring Events

Spring publishes many events automatically.

---

## Application Started

```java
@EventListener
public void started(
 ApplicationStartedEvent event) {

    System.out.println(
       "Application Started"
    );
}
```

---

## Application Ready

```java
@EventListener
public void ready(
 ApplicationReadyEvent event) {

    System.out.println(
       "Application Ready"
    );
}
```

---

## Context Refreshed

```java
@EventListener
public void refreshed(
 ContextRefreshedEvent event) {

}
```

---

# ApplicationReadyEvent Example

```java
@Component
public class StartupTasks {

    @EventListener(
      ApplicationReadyEvent.class
    )
    public void initialize() {

        System.out.println(
            "Running Startup Tasks"
        );
    }
}
```

---

Runs after application is fully started.

---

# Explicit Event Type

Instead of parameter type:

```java
@EventListener
public void handle(
      UserRegisteredEvent event) {

}
```

---

You can specify:

```java
@EventListener(
   UserRegisteredEvent.class
)
public void handle() {

}
```

---

Both work.

---

# Multiple Events

```java
@EventListener({
    UserRegisteredEvent.class,
    UserDeletedEvent.class
})
public void process() {

}
```

---

Triggered by either event.

---

# Conditional Events

Very important interview topic.

---

```java
@EventListener(
 condition =
 "#event.email.endsWith('@gmail.com')"
)
public void process(
       UserRegisteredEvent event) {

}
```

---

Meaning:

```text
Run Only For Gmail Users
```

---

Uses:

```text
SpEL
```

(Spring Expression Language)

---

# Returning Events

Interesting feature.

---

Listener:

```java
@EventListener
public UserProcessedEvent process(
        UserRegisteredEvent event) {

    return new UserProcessedEvent();
}
```

---

Spring automatically publishes:

```java
UserProcessedEvent
```

---

Execution:

```text
UserRegisteredEvent
      ↓
Listener
      ↓
UserProcessedEvent
      ↓
Other Listeners
```

---

# Async Event Listener

Very common in enterprise applications.

---

Enable async:

```java
@EnableAsync
```

---

Listener:

```java
@Async

@EventListener
public void sendEmail(
      UserRegisteredEvent event) {

}
```

---

Execution:

```text
Publish Event
      ↓
Return Immediately
      ↓
Background Thread
      ↓
Process Event
```

---

Useful for:

```text
Emails
Notifications
Audit Logging
Reports
```

---

# Transactional Events

Used with transactions.

---

```java
@TransactionalEventListener
```

---

Example:

```java
@TransactionalEventListener
public void process(
      OrderCreatedEvent event) {

}
```

---

Runs:

```text
After Transaction Commit
```

---

Common in:

```text
Order Systems
Payment Systems
Banking Systems
```

---

# Real Example

## Event

```java
public record OrderCreatedEvent(
        Long orderId) {
}
```

---

## Publisher

```java
publisher.publishEvent(
    new OrderCreatedEvent(
        orderId
    )
);
```

---

## Listener

```java
@EventListener
public void sendEmail(
      OrderCreatedEvent event) {

    System.out.println(
      "Order Email Sent"
    );
}
```

---

Flow:

```text
Order Created
      ↓
Publish Event
      ↓
Email Sent
```

---

# Internal Components

Spring uses:

```text
ApplicationEventPublisher
ApplicationEventMulticaster
EventListenerMethodProcessor
```

---

Execution:

```text
publishEvent()
      ↓
Multicaster
      ↓
Find Listeners
      ↓
Invoke Methods
```

---

# Common Use Cases

## User Registration

```java
@EventListener
```

---

## Email Sending

```java
@EventListener
```

---

## Audit Logging

```java
@EventListener
```

---

## Notifications

```java
@EventListener
```

---

## Startup Initialization

```java
@EventListener
```

---

## Cache Refresh

```java
@EventListener
```

---

# @EventListener vs Direct Method Call

Direct Call:

```java
userService.register();

emailService.send();
```

---

Characteristics:

```text
Tightly Coupled
```

---

Event Listener:

```java
publishEvent()
```

---

Characteristics:

```text
Loosely Coupled
Extensible
Maintainable
```

---

# Common Mistakes

## Mistake 1

Not Registering Bean

Wrong:

```java
public class EmailListener {

    @EventListener
    public void handle() {

    }
}
```

---

Need:

```java
@Component
```

or another Spring bean annotation.

---

# Mistake 2

Heavy Processing In Sync Listener

```java
@EventListener
```

---

Long-running work blocks publisher thread.

---

Use:

```java
@Async
```

---

# Mistake 3

Expecting Transaction Commit

```java
@EventListener
```

runs immediately.

---

Need:

```java
@TransactionalEventListener
```

---

# Common Interview Questions

## What is @EventListener?

Listens to Spring application events.

---

## How Is Event Published?

Using:

```java
ApplicationEventPublisher
```

---

## Can One Event Have Multiple Listeners?

Yes.

---

## Can EventListener Be Async?

Yes.

```java
@Async
@EventListener
```

---

## What Is The Difference Between EventListener And TransactionalEventListener?

`@EventListener`

runs immediately.

---

`@TransactionalEventListener`

runs after transaction phase.

---

## Does Spring Use Reflection?

Yes.

Spring scans methods and registers listeners automatically.

---

# Enterprise Best Practice

Use events for:

```text
Email Notifications
Audit Logs
Order Processing
Cache Updates
Application Startup Tasks
```

---

Combine:

```java
@Async
@EventListener
```

for non-critical background tasks.

---

Use:

```java
@TransactionalEventListener
```

for transaction-sensitive operations.

---

# Key Points To Remember

- `@EventListener` listens to Spring events.
- Events are published using `ApplicationEventPublisher`.
- Supports custom and built-in Spring events.
- One event can have multiple listeners.
- Supports SpEL conditions.
- Supports async execution.
- Can publish new events by returning objects.
- Promotes loose coupling.
- Uses Spring's event infrastructure internally.
- Frequently asked Spring Boot interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EventListener` | Listen To Events |
| `@TransactionalEventListener` | Listen After Transaction Phase |
| `@Async` | Run Listener In Background |
| `@EnableAsync` | Enable Async Processing |

---

# Interview Shortcut

```java
@EventListener
public void handle(
    UserRegisteredEvent event) {

    sendEmail();
}
```

Execution:

```text
User Registered
      ↓
Publish Event
      ↓
Spring Event Dispatcher
      ↓
Listener Triggered
      ↓
Send Email
```

Think of it as:

```java
publisher.publishEvent(event);
```

automatically calling:

```java
listener.handle(event);
```

for every matching listener.

---

# Real Production Scenario

```java
publisher.publishEvent(
    new OrderCreatedEvent(
        orderId
    )
);
```

Listeners:

```java
@EventListener
sendEmail()
```

```java
@EventListener
createAuditLog()
```

```java
@EventListener
updateAnalytics()
```

Flow:

```text
Order Created
      ↓
Publish Event
      ↓
Email Service
      ↓
Audit Service
      ↓
Analytics Service
```

Result:

```text
Loose Coupling
Better Scalability
Cleaner Architecture
```

`@EventListener` is Spring's event-handling annotation that allows a bean method to automatically react to application events, enabling a clean, loosely coupled, event-driven architecture.