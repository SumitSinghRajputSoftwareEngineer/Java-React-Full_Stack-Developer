# Mediator Design Pattern

> **Goal:** Understand Mediator Design Pattern from scratch, why it exists, how it works internally, Colleagues, Concrete Mediator, Event Coordination, Chat Room example, Air Traffic Control example, GUI examples, Spring usage, advantages, disadvantages, interview questions, and how it differs from Observer, Facade, and Chain of Responsibility.

---

# 1. What Problem Does Mediator Pattern Solve?

Suppose we have 4 classes:

```text
User1
User2
User3
User4
```

---

Without Mediator:

Every user communicates with every other user.

```text
User1 ↔ User2
User1 ↔ User3
User1 ↔ User4

User2 ↔ User3
User2 ↔ User4

User3 ↔ User4
```

---

Dependencies grow rapidly.

---

For 4 users:

```text
6 connections
```

For 10 users:

```text
45 connections
```

For 100 users:

```text
4950 connections
```

---

This creates:

```text
Tight Coupling
Complex Communication
Hard Maintenance
```

---

Need a central object to coordinate communication.

---

This is exactly what Mediator Pattern does.

---

# 2. Definition

> Mediator Pattern defines an object that encapsulates how a set of objects interact with each other.

---

# 3. Simple Meaning

Instead of:

```text
User1 ↔ User2
User1 ↔ User3
User1 ↔ User4
...
```

Use:

```text
          Mediator
         /   |   \
        /    |    \
    User1 User2 User3
```

---

Objects communicate through:

```text
Mediator
```

---

Not directly.

---

# 4. Real-Life Examples

---

## Example 1: Air Traffic Control Tower

Imagine:

```text
Plane A
Plane B
Plane C
Plane D
```

---

Without ATC:

Each plane talks to every other plane.

Impossible.

---

Instead:

```text
Plane
   ↓
ATC Tower
   ↓
Other Plane
```

---

ATC acts as:

```text
Mediator
```

---

# Example 2: Chat Room

Users don't communicate directly.

---

Flow:

```text
User
  ↓
Chat Room
  ↓
Other Users
```

---

Chat Room is mediator.

---

# Example 3: Railway Control Room

Trains coordinate via:

```text
Control Center
```

---

Mediator.

---

# Example 4: GUI Dialog Box

Button click affects:

```text
Textbox
Checkbox
Dropdown
```

---

Instead of components talking directly:

```text
DialogMediator
```

coordinates them.

---

# 5. Core Idea

Move communication logic from:

```text
Objects
```

to

```text
Mediator
```

---

Objects become simpler.

---

# 6. Components

---

## 1. Mediator

Defines communication methods.

---

## 2. Concrete Mediator

Actual coordination logic.

---

## 3. Colleague

Objects that communicate.

---

## 4. Concrete Colleague

Actual participating objects.

---

# 7. UML Structure

```text
           Mediator
               ↑
       ----------------
       ↑              ↑

   User1          User2

       ↑              ↑

       ----ChatRoom----
```

---

# 8. Real-World Example

# Chat Room

---

## Step 1: Mediator Interface

```java
public interface ChatMediator {

    void sendMessage(
            String message,
            User sender);
}
```

---

Mediator controls communication.

---

# Step 2: Colleague

```java
public abstract class User {

    protected ChatMediator mediator;

    protected String name;

    public User(
            ChatMediator mediator,
            String name) {

        this.mediator = mediator;
        this.name = name;
    }

    public abstract void send(
            String message);

    public abstract void receive(
            String message);
}
```

---

Users know mediator.

---

Users DO NOT know each other.

---

# Step 3: Concrete User

```java
public class ChatUser
        extends User {

    public ChatUser(
            ChatMediator mediator,
            String name) {

        super(mediator, name);
    }

    @Override
    public void send(
            String message) {

        mediator.sendMessage(
                message,
                this);
    }

    @Override
    public void receive(
            String message) {

        System.out.println(
                name + " received: "
                        + message);
    }
}
```

---

# Step 4: Concrete Mediator

```java
public class ChatRoom
        implements ChatMediator {

    private List<User> users =
            new ArrayList<>();

    public void addUser(
            User user) {

        users.add(user);
    }

    @Override
    public void sendMessage(
            String message,
            User sender) {

        for(User user : users) {

            if(user != sender) {

                user.receive(
                        message);
            }
        }
    }
}
```

---

Mediator coordinates everything.

---

# Step 5: Client

```java
ChatRoom room =
        new ChatRoom();

User u1 =
        new ChatUser(
                room,
                "John");

User u2 =
        new ChatUser(
                room,
                "Alice");

room.addUser(u1);
room.addUser(u2);

u1.send("Hello");
```

---

Output:

```text
Alice received: Hello
```

---

# 9. Internal Working

Flow:

```text
John
  ↓
ChatRoom
  ↓
Alice
```

---

Notice:

```text
John
```

never knows:

```text
Alice
```

---

Everything goes through:

```text
Mediator
```

---

# 10. Why Mediator?

Without mediator:

```java
user1.send(user2);
user1.send(user3);
user1.send(user4);
```

---

Dependencies explode.

---

With mediator:

```java
mediator.send(...)
```

---

Only one dependency.

---

# 11. Air Traffic Control Example

Most famous example.

---

Planes:

```text
Plane A
Plane B
Plane C
```

---

All communicate with:

```text
ATC Tower
```

---

Never directly.

---

Flow:

```text
Plane A
  ↓
ATC
  ↓
Plane B
```

---

Classic Mediator Pattern.

---

# 12. GUI Dialog Example

Suppose:

```text
Button
Textbox
Checkbox
Dropdown
```

---

When button clicked:

```text
Disable Textbox

Enable Dropdown

Clear Checkbox
```

---

Without mediator:

```text
Button knows everyone
```

---

Bad design.

---

Mediator handles:

```text
Component Coordination
```

---

Very common GUI usage.

---

# 13. Event Coordination

Mediator often acts as:

```text
Event Coordinator
```

---

Example:

```text
Button Click
```

↓

```text
Mediator
```

↓

```text
Update Components
```

---

# 14. Java Examples

No direct Mediator API exists.

---

But many frameworks use the concept.

---

# 15. Spring Framework Example

:contentReference[oaicite:0]{index=0}

Spring IoC Container behaves somewhat like a mediator.

---

Beans communicate through:

```text
Application Context
```

---

instead of directly creating each other.

---

Conceptually similar.

---

# 16. MVC Architecture

Controller often acts as:

```text
Mediator
```

---

Flow:

```text
View
 ↓
Controller
 ↓
Model
```

---

Controller coordinates interactions.

---

# 17. Event Bus Example

Popular frameworks:

```text
Guava EventBus

Spring Events
```

---

Publisher:

```text
Send Event
```

↓

```text
Mediator/Event Bus
```

↓

```text
Subscribers
```

---

Mediator-like behavior.

---

# 18. Advantages

---

## 1. Reduced Coupling

Biggest benefit.

---

## 2. Centralized Communication

All interactions in one place.

---

## 3. Easier Maintenance

Rules changed in mediator only.

---

## 4. Better Reusability

Colleagues become independent.

---

## 5. Follows Single Responsibility Principle

Communication logic separated.

---

# 19. Disadvantages

---

## 1. Mediator Can Become Huge

Most common problem.

---

Called:

```text
God Object
```

---

Mediator handles everything.

---

## 2. Complex Logic Centralized

Too many rules.

---

## 3. Harder To Debug

Many interactions routed through mediator.

---

# 20. Mediator vs Observer

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

Publisher usually doesn't coordinate interactions.

---

## Mediator

Objects communicate through central object.

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

# 21. Mediator vs Facade

---

## Facade

Simplifies subsystem.

---

Example:

```java
orderFacade.placeOrder()
```

---

## Mediator

Coordinates communication.

---

Example:

```java
chatRoom.sendMessage()
```

---

### Memory Trick

```text
Facade
      ↓
Simplify

Mediator
      ↓
Coordinate
```

---

# 22. Mediator vs Chain of Responsibility

---

## Chain Of Responsibility

Request travels through handlers.

---

```text
Manager
 ↓
Director
 ↓
CEO
```

---

## Mediator

Request goes to central coordinator.

---

```text
User
 ↓
Mediator
 ↓
User
```

---

### Memory Trick

```text
CoR
     ↓
Pass Along

Mediator
     ↓
Central Hub
```

---

# 23. Mediator vs Observer vs CoR

| Pattern | Purpose |
|----------|----------|
| Observer | Notify Many |
| Mediator | Coordinate Many |
| Chain of Responsibility | Pass Request |
| Command | Encapsulate Request |
| Proxy | Control Access |

---

# 24. When To Use Mediator

Use when:

- Many objects communicate
- Dependencies are growing
- Centralized communication needed
- Event coordination required

Examples:

```text
Chat Rooms
ATC Towers
GUI Components
Controllers
Event Buses
```

---

# 25. When NOT To Use

Avoid when:

- Very few objects interact
- Communication is simple
- Mediator becomes God Object

---

# 26. Interview Questions

### Q1. What problem does Mediator solve?

Reduces coupling between communicating objects.

---

### Q2. What are Colleagues?

Objects that communicate through mediator.

---

### Q3. What is Concrete Mediator?

Actual coordinator implementation.

---

### Q4. Give real-life examples.

```text
ATC Tower

Chat Room

Railway Control Center

GUI Dialog
```

---

### Q5. Biggest advantage?

```text
Loose Coupling
```

---

### Q6. Biggest disadvantage?

```text
God Object Problem
```

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

### Q8. Give Spring example.

```text
Application Context

Event Bus
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
```

---

# Quick Revision

```text
Mediator Design Pattern
-----------------------

Purpose:
Centralize communication.

Components:

1. Mediator
2. Concrete Mediator
3. Colleague
4. Concrete Colleague

Examples:

✔ Chat Room
✔ ATC Tower
✔ Railway Control Center
✔ GUI Dialogs

Advantages:

✔ Loose Coupling
✔ Centralized Rules
✔ Easier Maintenance

Disadvantages:

✘ God Object Risk
✘ Centralized Complexity

Remember:

Observer
      ↓
Notify Many

Mediator
      ↓
Coordinate Many

Chain Of Responsibility
      ↓
Pass Request
```