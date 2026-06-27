# Observer Design Pattern

> **Goal:** Understand Observer Design Pattern from scratch, why it exists, how it works internally, Subject, Observer, Push vs Pull Model, Java Observer implementations, Event-Driven Architecture, Spring Events, Kafka-like pub-sub concepts, advantages, disadvantages, interview questions, and how it differs from Mediator and Chain of Responsibility.

---

# 1. What Problem Does Observer Pattern Solve?

Suppose you have a YouTube channel.

Subscribers:

```text
Sumit
Rahul
Aman
Priya
```

---

When a new video is uploaded:

```text
All subscribers should be notified.
```

---

Without Observer Pattern:

```java
subscriber1.notify();

subscriber2.notify();

subscriber3.notify();

subscriber4.notify();
```

---

Problem:

Whenever a new subscriber joins:

```text
Code must change.
```

---

This violates:

```text
Open/Closed Principle
```

---

Need a way where:

```text
Publisher doesn't care
who the subscribers are.
```

---

This is exactly what Observer Pattern solves.

---

# 2. Definition

> Observer Pattern defines a one-to-many dependency between objects so that when one object changes state, all dependent objects are automatically notified.

---

# 3. Simple Meaning

One object:

```text
Publisher
```

changes state.

---

Many objects:

```text
Subscribers
```

get notified automatically.

---

# 4. Real-Life Examples

---

## Example 1: YouTube Channel

```text
Channel uploads video
       ↓
All subscribers notified
```

---

Perfect Observer Pattern.

---

# Example 2: Instagram

```text
User posts story
       ↓
Followers notified
```

---

# Example 3: Stock Market

```text
Stock Price Changes
       ↓
Investors notified
```

---

# Example 4: Weather Station

```text
Temperature Changes
       ↓
Displays Updated
```

---

Classic textbook example.

---

# 5. Core Idea

Instead of:

```text
Publisher
   ↓
Subscriber1

Publisher
   ↓
Subscriber2

Publisher
   ↓
Subscriber3
```

Use:

```text
Subscribers Register
          ↓
      Publisher
          ↓
Notify All
```

---

Publisher doesn't know specific implementations.

---

# 6. Components

---

## 1. Subject (Publisher)

Maintains observer list.

---

## 2. Observer (Subscriber)

Receives updates.

---

## 3. Concrete Subject

Actual publisher.

---

## 4. Concrete Observer

Actual subscriber.

---

# 7. UML Structure

```text
            Subject
                ↑
         ---------------
         ↑      ↑      ↑

     Observer Observer Observer
```

---

# 8. Real-World Example

# YouTube Notification System

---

## Step 1: Observer Interface

```java
public interface Subscriber {

    void update(
            String videoTitle);
}
```

---

All observers implement:

```java
update()
```

---

# Step 2: Concrete Observer

```java
public class User
        implements Subscriber {

    private String name;

    public User(String name) {

        this.name = name;
    }

    @Override
    public void update(
            String videoTitle) {

        System.out.println(
                name +
                " received notification: "
                + videoTitle);
    }
}
```

---

# Step 3: Subject Interface

```java
public interface Channel {

    void subscribe(
            Subscriber subscriber);

    void unsubscribe(
            Subscriber subscriber);

    void notifySubscribers();
}
```

---

# Step 4: Concrete Subject

```java
public class YoutubeChannel
        implements Channel {

    private List<Subscriber>
            subscribers =
            new ArrayList<>();

    private String videoTitle;

    @Override
    public void subscribe(
            Subscriber subscriber) {

        subscribers.add(
                subscriber);
    }

    @Override
    public void unsubscribe(
            Subscriber subscriber) {

        subscribers.remove(
                subscriber);
    }

    @Override
    public void notifySubscribers() {

        for(Subscriber subscriber
                : subscribers) {

            subscriber.update(
                    videoTitle);
        }
    }

    public void uploadVideo(
            String title) {

        this.videoTitle = title;

        notifySubscribers();
    }
}
```

---

# Step 5: Client

```java
YoutubeChannel channel =
        new YoutubeChannel();

Subscriber s1 =
        new User("Sumit");

Subscriber s2 =
        new User("Rahul");

channel.subscribe(s1);
channel.subscribe(s2);

channel.uploadVideo(
        "Observer Pattern");
```

---

Output:

```text
Sumit received notification:
Observer Pattern

Rahul received notification:
Observer Pattern
```

---

# 9. Internal Working

Flow:

```text
Upload Video
      ↓
Subject State Changes
      ↓
notifySubscribers()
      ↓
update()
      ↓
Observers Receive Event
```

---

# 10. Push Model

Most common implementation.

---

Publisher sends data directly.

---

Example:

```java
update(videoTitle);
```

---

Publisher pushes information.

---

Diagram:

```text
Publisher
     ↓
Data
     ↓
Observer
```

---

# 11. Pull Model

Publisher only notifies.

---

Observer fetches data.

---

Example:

```java
update();
```

---

Then:

```java
subject.getVideoTitle();
```

---

Observer pulls data.

---

Diagram:

```text
Publisher
     ↓
Notification

Observer
     ↓
Get Data
```

---

# 12. Push vs Pull Model

| Push | Pull |
|--------|--------|
| Publisher sends data | Observer fetches data |
| Faster | More Flexible |
| More coupling | Less coupling |

---

Important interview topic.

---

# 13. Dynamic Subscription

Observers can:

```java
subscribe()
```

and

```java
unsubscribe()
```

at runtime.

---

Very powerful feature.

---

# 14. Java Built-In Observer

Older Java versions provided:

```java
java.util.Observer
```

and

```java
java.util.Observable
```

---

Both are deprecated.

---

Reason:

```text
Design Limitations
```

---

Interview point.

---

# 15. Modern Java Alternatives

Use:

```java
PropertyChangeListener
```

---

Or:

```java
EventListener
```

---

Or custom Observer implementation.

---

# 16. Event-Driven Architecture

Observer Pattern is foundation of:

```text
Event Driven Systems
```

---

Flow:

```text
Event Occurs
      ↓
Listeners Notified
```

---

# 17. GUI Example

Button Click:

```text
Button
   ↓
Listeners
```

---

Example:

```java
ActionListener
```

---

Observer Pattern.

---

# 18. Spring Framework Example

:contentReference[oaicite:0]{index=0}

Spring Events are classic Observer Pattern.

---

Publisher:

```java
applicationEventPublisher
```

---

Listener:

```java
@EventListener
```

---

Example:

```java
publisher.publishEvent(
        new OrderCreatedEvent());
```

---

Listener:

```java
@EventListener
public void handle(
        OrderCreatedEvent event) {
}
```

---

Perfect Observer Pattern.

---

# 19. Spring Boot Example

Order Created:

```text
Order Service
      ↓
Publish Event
      ↓
Email Service
      ↓
Notification Service
      ↓
Audit Service
```

---

Services loosely coupled.

---

# 20. Kafka and Pub-Sub

:contentReference[oaicite:1]{index=1}

Observer concepts appear heavily.

---

Publisher:

```text
Producer
```

---

Observer:

```text
Consumer
```

---

Flow:

```text
Producer
   ↓
Topic
   ↓
Consumers
```

---

Not exactly Observer Pattern.

---

But same idea:

```text
One-to-Many Notification
```

---

# 21. Advantages

---

## 1. Loose Coupling

Publisher doesn't know observer implementation.

---

## 2. Dynamic Subscription

Observers added/removed anytime.

---

## 3. Supports Broadcast Communication

Notify many objects.

---

## 4. Follows Open/Closed Principle

New observers without modifying publisher.

---

# 22. Disadvantages

---

## 1. Notification Overhead

Many observers.

---

## 2. Unexpected Updates

Hard to track notification chain.

---

## 3. Memory Leaks

Forgotten observers remain registered.

---

Very common issue.

---

# 23. Observer vs Mediator

Most asked interview question.

---

## Observer

One publisher.

Many subscribers.

---

```text
Publisher
   ↓
Observers
```

---

## Mediator

Many objects communicate via central object.

---

```text
Object
   ↓
Mediator
   ↓
Object
```

---

### Memory Trick

```text
Observer
      ↓
Notify Many

Mediator
      ↓
Coordinate Many
```

---

# 24. Observer vs Chain Of Responsibility

---

## Observer

All observers notified.

---

```text
A
B
C
```

all receive event.

---

## CoR

Only one handler may process request.

---

```text
Manager
 ↓
Director
 ↓
CEO
```

---

### Memory Trick

```text
Observer
      ↓
Broadcast

CoR
      ↓
Pass Along
```

---

# 25. Observer vs Pub-Sub

Advanced interview topic.

---

## Observer

Subject knows observers.

---

Example:

```java
List<Observer>
```

---

## Pub-Sub

Publisher doesn't know subscribers.

---

Message broker exists.

---

Example:

```text
Kafka
RabbitMQ
```

---

### Memory Trick

```text
Observer
      ↓
Direct Notification

Pub-Sub
      ↓
Broker-Based
```

---

# 26. When To Use Observer

Use when:

- One-to-many notification required
- Event-driven systems needed
- Dynamic listeners required

Examples:

```text
YouTube
Instagram
Stock Market
Spring Events
GUI Events
```

---

# 27. When NOT To Use

Avoid when:

- Very simple communication
- Notification chain becomes huge
- Tight ordering required

---

# 28. Interview Questions

### Q1. What problem does Observer solve?

Automatic notification of dependent objects.

---

### Q2. What are participants?

```text
Subject

Observer

Concrete Subject

Concrete Observer
```

---

### Q3. What is Push Model?

Publisher sends data.

---

### Q4. What is Pull Model?

Observer fetches data.

---

### Q5. Give Spring example.

```text
ApplicationEventPublisher

@EventListener
```

---

### Q6. Why were Observable and Observer deprecated?

Design limitations and inheritance issues.

---

### Q7. Difference Between Observer and Mediator?

Observer:

```text
Notify Many
```

Mediator:

```text
Coordinate Many
```

---

### Q8. Difference Between Observer and Pub-Sub?

Observer:

```text
Direct Registration
```

Pub-Sub:

```text
Broker-Based
```

---

# Real-Life Memory Trick

```text
Singleton
     ↓
One Object

Factory
     ↓
Create Object

Builder
     ↓
Build Object

Prototype
     ↓
Clone Object

Adapter
     ↓
Convert Interface

Bridge
     ↓
Separate Abstraction

Composite
     ↓
Tree Structure

Facade
     ↓
Hide Complexity

Decorator
     ↓
Add Behavior

Flyweight
     ↓
Share State

Chain Of Responsibility
     ↓
Pass Request

Proxy
     ↓
Control Access

Command
     ↓
Encapsulate Request

Interpreter
     ↓
Interpret Grammar

Iterator
     ↓
Traverse Collection

Mediator
     ↓
Coordinate Communication

Memento
     ↓
Save State

Observer
     ↓
Notify Dependents
```

---

# Quick Revision

```text
Observer Design Pattern
-----------------------

Purpose:
One-to-many notification.

Participants:

1. Subject
2. Observer
3. Concrete Subject
4. Concrete Observer

Models:

✔ Push Model
✔ Pull Model

Examples:

✔ YouTube Subscribers
✔ Instagram Followers
✔ Stock Market
✔ Spring Events
✔ GUI Listeners

Advantages:

✔ Loose Coupling
✔ Dynamic Subscription
✔ Event Driven

Disadvantages:

✘ Notification Overhead
✘ Memory Leaks
✘ Debugging Complexity

Spring Example:

ApplicationEventPublisher
@EventListener

Remember:

Observer
      ↓
Notify Many

Mediator
      ↓
Coordinate Many

CoR
      ↓
Pass Request
```