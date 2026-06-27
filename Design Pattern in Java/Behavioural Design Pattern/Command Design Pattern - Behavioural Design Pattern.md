# Command Design Pattern

> **Goal:** Understand Command Design Pattern from scratch, why it exists, how it works internally, Invoker, Receiver, Command Queue, Undo/Redo, Macro Commands, Spring usage, advantages, disadvantages, interview questions, and how it differs from Strategy, Chain of Responsibility, and Observer.

---

# 1. What Problem Does Command Pattern Solve?

Suppose you have a Remote Control.

Buttons:

```text
ON
OFF
VOLUME UP
VOLUME DOWN
```

---

Without Command Pattern:

```java
class Remote {

    private TV tv;

    public void onButtonPressed() {
        tv.turnOn();
    }

    public void offButtonPressed() {
        tv.turnOff();
    }
}
```

---

Problem:

If tomorrow we want:

```text
Fan
AC
Music System
Projector
```

---

We must modify:

```java
Remote
```

again and again.

---

This violates:

```text
Open/Closed Principle
```

---

Need a way to make buttons independent of devices.

---

# 2. Definition

> Command Pattern encapsulates a request as an object, thereby allowing requests to be parameterized, queued, logged, and undone.

---

# 3. Simple Meaning

Instead of:

```text
Remote
   ↓
TV
```

---

Create:

```text
Remote
   ↓
Command
   ↓
TV
```

---

Remote knows:

```text
Execute Command
```

---

It does NOT know:

```text
TV
Fan
AC
Projector
```

---

# 4. Real-Life Examples

---

## Example 1: Restaurant Order

Customer places order:

```text
Burger
```

---

Waiter does NOT cook.

---

Waiter creates:

```text
Order Ticket
```

---

Chef executes ticket.

---

Mapping:

```text
Customer = Client

Order Ticket = Command

Chef = Receiver

Waiter = Invoker
```

---

Perfect Command Pattern.

---

# Example 2: Remote Control

Button stores:

```text
TV ON Command
```

---

When pressed:

```text
Execute Command
```

---

# Example 3: Undo/Redo

Text editor:

```text
Cut
Copy
Paste
Delete
```

---

Each action stored as command.

---

Can be:

```text
Undo
Redo
```

---

# 5. Core Idea

Convert:

```java
tv.turnOn();
```

into:

```java
command.execute();
```

---

The request becomes an object.

---

# 6. Components

There are 5 important participants.

---

## 1. Client

Creates command.

---

## 2. Command

Interface.

---

## 3. Concrete Command

Actual command implementation.

---

## 4. Receiver

Performs work.

---

## 5. Invoker

Triggers command.

---

# 7. UML Structure

```text
Client
  ↓
Command
  ↓
ConcreteCommand
  ↓
Receiver

Invoker
  ↓
Command
```

---

# 8. Real-World Example

# TV Remote

---

## Step 1: Receiver

```java
public class TV {

    public void turnOn() {

        System.out.println(
                "TV ON");
    }

    public void turnOff() {

        System.out.println(
                "TV OFF");
    }
}
```

---

Receiver contains actual business logic.

---

# Step 2: Command Interface

```java
public interface Command {

    void execute();
}
```

---

Most important method:

```java
execute()
```

---

# Step 3: Concrete Command

## TV ON Command

```java
public class TvOnCommand
        implements Command {

    private TV tv;

    public TvOnCommand(
            TV tv) {

        this.tv = tv;
    }

    @Override
    public void execute() {

        tv.turnOn();
    }
}
```

---

## TV OFF Command

```java
public class TvOffCommand
        implements Command {

    private TV tv;

    public TvOffCommand(
            TV tv) {

        this.tv = tv;
    }

    @Override
    public void execute() {

        tv.turnOff();
    }
}
```

---

Notice:

Command object stores:

```java
private TV tv;
```

---

This receiver reference is extremely important.

---

# Step 4: Invoker

```java
public class RemoteControl {

    private Command command;

    public void setCommand(
            Command command) {

        this.command = command;
    }

    public void pressButton() {

        command.execute();
    }
}
```

---

Invoker never knows:

```text
TV
Fan
AC
```

---

Only knows:

```java
command.execute();
```

---

# Step 5: Client

```java
TV tv = new TV();

Command onCommand =
        new TvOnCommand(tv);

RemoteControl remote =
        new RemoteControl();

remote.setCommand(onCommand);

remote.pressButton();
```

---

Output:

```text
TV ON
```

---

# 9. Internal Working

Flow:

```text
Client
  ↓
RemoteControl
  ↓
TvOnCommand
  ↓
TV
```

---

Execution:

```java
remote.pressButton()
```

↓

```java
command.execute()
```

↓

```java
tv.turnOn()
```

---

# 10. Why Command Object?

Because request becomes:

```text
First-Class Object
```

---

Meaning:

We can:

```text
Store It
Queue It
Undo It
Serialize It
Log It
```

---

Huge flexibility.

---

# 11. Command Queue

Commands can be queued.

---

Example:

```java
Queue<Command>
```

---

Add:

```java
TvOnCommand

FanOnCommand

AcOnCommand
```

---

Process later.

---

Used in:

```text
Task Scheduling
Job Processing
Messaging Systems
```

---

# 12. Undo Functionality

One of the biggest reasons Command Pattern exists.

---

Extend interface:

```java
public interface Command {

    void execute();

    void undo();
}
```

---

Example:

```java
public class TvOnCommand
        implements Command {

    public void execute() {

        tv.turnOn();
    }

    public void undo() {

        tv.turnOff();
    }
}
```

---

Now:

```text
Undo Possible
```

---

# 13. Undo Stack

Store commands:

```java
Stack<Command>
```

---

Flow:

```text
Execute Command
        ↓
Push Into Stack
```

---

Undo:

```text
Pop Command
      ↓
undo()
```

---

Used in:

```text
Word
Photoshop
VS Code
IntelliJ
```

---

# 14. Redo Functionality

Maintain:

```java
undoStack

redoStack
```

---

Flow:

```text
Undo
   ↓
Move To Redo Stack
```

---

Redo:

```text
Execute Again
```

---

# 15. Macro Command

Advanced Interview Topic.

---

Suppose:

```text
Movie Mode
```

requires:

```text
Turn On TV
Turn On Sound System
Turn Off Lights
```

---

Create:

```java
MacroCommand
```

---

```java
public class MacroCommand
        implements Command {

    private List<Command> commands;

    public void execute() {

        for(Command cmd : commands) {

            cmd.execute();
        }
    }
}
```

---

One command executes many commands.

---

# 16. Command Logging

Because command is object:

```java
Save To Database
```

possible.

---

Useful for:

```text
Audit Logs
Transaction History
```

---

# 17. Command Scheduling

Commands can execute later.

---

Example:

```java
scheduler.schedule(
        command,
        tomorrow);
```

---

Very common in enterprise systems.

---

# 18. Java Examples

Several Java APIs use Command concepts.

---

## Runnable

Most famous example.

---

```java
Runnable task =
        () -> System.out.println(
                "Hello");
```

---

Command:

```java
run()
```

---

Receiver:

```text
Business Logic
```

---

Invoker:

```java
Thread
```

---

Classic Command Pattern.

---

# Example

```java
new Thread(task).start();
```

---

Flow:

```text
Thread
  ↓
Runnable
  ↓
Business Logic
```

---

# 19. Java Swing

Buttons store:

```java
ActionListener
```

---

When clicked:

```java
actionPerformed()
```

---

Command-like behavior.

---

# 20. Spring Framework Examples

---

## TaskExecutor

:contentReference[oaicite:0]{index=0}

Example:

```java
taskExecutor.execute(
        runnable);
```

---

Runnable acts as command.

---

# 21. Spring Batch

Jobs stored as objects.

---

Commands executed later.

---

Command concepts heavily used.

---

# 22. Messaging Systems

Examples:

```text
Kafka

RabbitMQ
```

---

Messages often represent commands.

---

Example:

```text
Create Order

Send Email

Generate Invoice
```

---

Executed asynchronously.

---

# 23. Advantages

---

## 1. Loose Coupling

Invoker doesn't know receiver.

---

## 2. Supports Undo/Redo

Biggest benefit.

---

## 3. Supports Queueing

Commands stored and executed later.

---

## 4. Supports Logging

Easy auditing.

---

## 5. Follows Open/Closed Principle

New commands without modifying existing code.

---

# 24. Disadvantages

---

## 1. More Classes

Every operation becomes command class.

---

## 2. Increased Complexity

Many small objects.

---

## 3. Extra Indirection

More layers.

---

# 25. Command vs Strategy

Most asked interview question.

---

## Strategy

Encapsulates:

```text
Algorithm
```

---

Example:

```text
UPI Payment

Card Payment

PayPal Payment
```

---

Choose one algorithm.

---

## Command

Encapsulates:

```text
Request
```

---

Example:

```text
Turn On TV

Delete File

Send Email
```

---

### Memory Trick

```text
Strategy
      ↓
How To Do

Command
      ↓
What To Do
```

---

# 26. Command vs Chain of Responsibility

---

## Command

One receiver executes.

---

Example:

```text
Turn On TV
```

---

## CoR

Request moves through handlers.

---

Example:

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
Command
      ↓
Execute Request

CoR
      ↓
Pass Request
```

---

# 27. Command vs Observer

---

## Observer

One event.

Many listeners.

---

```text
Publisher
    ↓
Many Observers
```

---

## Command

One request.

One execution.

---

### Memory Trick

```text
Observer
      ↓
Notify Many

Command
      ↓
Execute One
```

---

# 28. Command vs Factory

---

## Factory

Creates object.

---

## Command

Executes action.

---

Different purposes.

---

# 29. When To Use Command Pattern

Use when:

- Undo/Redo needed
- Queueing needed
- Logging needed
- Scheduling needed
- Request encapsulation needed

Examples:

```text
Remote Controls

Text Editors

Task Scheduling

Workflow Engines

Messaging Systems
```

---

# 30. When NOT To Use

Avoid when:

- Simple direct calls sufficient
- No need for undo, queue, scheduling

---

# 31. Interview Questions

### Q1. What problem does Command solve?

Encapsulates request as an object.

---

### Q2. What are the participants?

```text
Client

Invoker

Command

Concrete Command

Receiver
```

---

### Q3. Why is Command useful?

Supports:

```text
Undo
Redo
Queue
Scheduling
Logging
```

---

### Q4. Give Java examples.

```text
Runnable

ActionListener
```

---

### Q5. Difference Between Strategy and Command?

Strategy:

```text
How To Execute
```

Command:

```text
What To Execute
```

---

### Q6. What is a Macro Command?

A command containing multiple commands.

---

### Q7. How is Undo implemented?

Using:

```java
undo()
```

and:

```java
Stack<Command>
```

---

### Q8. Give Spring example.

```text
TaskExecutor + Runnable
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
As Object
```

---

# Quick Revision

```text
Command Design Pattern
----------------------

Purpose:
Encapsulate request as object.

Structure:

Client
  ↓
Invoker
  ↓
Command
  ↓
Receiver

Key Features:

✔ Undo
✔ Redo
✔ Queueing
✔ Scheduling
✔ Logging

Important Classes:

Command
ConcreteCommand
Invoker
Receiver

Java Examples:

Runnable
ActionListener

Advantages:

✔ Loose Coupling
✔ Undo Support
✔ Queue Support
✔ Logging

Disadvantages:

✘ Many Classes
✘ More Complexity

Remember:

Strategy
     ↓
How To Do

Command
     ↓
What To Do

CoR
     ↓
Who Will Handle
```