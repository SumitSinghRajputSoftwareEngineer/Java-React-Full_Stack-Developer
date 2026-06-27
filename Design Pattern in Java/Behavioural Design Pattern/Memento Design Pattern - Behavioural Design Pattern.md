# Memento Design Pattern

> **Goal:** Understand Memento Design Pattern from scratch, why it exists, how it works internally, Originator, Memento, Caretaker, Undo/Redo implementation, Snapshot Pattern, Deep Copy vs Shallow Copy, Serialization-based snapshots, real-world examples, advantages, disadvantages, interview questions, and how it differs from Command and Prototype.

---

# 1. What Problem Does Memento Pattern Solve?

Suppose you are writing a text editor.

User types:

```text
Hello
```

Then:

```text
Hello Java
```

Then:

```text
Hello Java Spring
```

---

Now user presses:

```text
CTRL + Z
```

Expected output:

```text
Hello Java
```

---

Press again:

```text
Hello
```

---

Question:

How do we restore old states?

---

One solution:

```java
String oldText1;
String oldText2;
String oldText3;
```

---

Problem:

As states increase:

```text
Memory Management Difficult
Code Becomes Messy
```

---

Need a structured way to save and restore object state.

---

This is exactly what Memento Pattern solves.

---

# 2. Definition

> Memento Pattern captures and externalizes an object's internal state so that the object can be restored later without violating encapsulation.

---

# 3. Simple Meaning

Take a snapshot of an object.

---

Later:

```text
Restore Snapshot
```

---

Exactly like:

```text
Game Save Point

Undo Button

Database Backup

System Restore Point
```

---

# 4. Real-Life Examples

---

## Example 1: Text Editor Undo

```text
Type Text
Save Snapshot
Undo
Restore Snapshot
```

---

# Example 2: Video Game

Before boss fight:

```text
Save Game
```

---

After losing:

```text
Load Previous Save
```

---

Memento Pattern.

---

# Example 3: Photoshop

```text
Undo
Redo
History
```

---

Uses snapshots.

---

# Example 4: Database Backup

```text
Take Backup
Restore Backup
```

---

Same concept.

---

# 5. Core Idea

Store object state in a separate object.

---

Instead of:

```text
Object
```

managing all history.

---

Use:

```text
Memento
```

to store snapshots.

---

# 6. Components

There are 3 important participants.

---

## 1. Originator

Object whose state must be saved.

---

Example:

```java
TextEditor
```

---

## 2. Memento

Stores snapshot.

---

Example:

```java
EditorState
```

---

## 3. Caretaker

Maintains history.

---

Example:

```java
UndoManager
```

---

# 7. UML Structure

```text
Client
  ↓
Caretaker
  ↓
Memento

Originator
   ↑
Save / Restore
```

---

# 8. Real-World Example

# Text Editor Undo

---

## Step 1: Memento

```java
public class EditorMemento {

    private final String content;

    public EditorMemento(
            String content) {

        this.content = content;
    }

    public String getContent() {

        return content;
    }
}
```

---

Stores snapshot.

---

Notice:

```java
final
```

---

Snapshot should be immutable.

---

# Step 2: Originator

```java
public class TextEditor {

    private String content;

    public void write(
            String content) {

        this.content = content;
    }

    public String getContent() {

        return content;
    }

    public EditorMemento save() {

        return new EditorMemento(
                content);
    }

    public void restore(
            EditorMemento memento) {

        this.content =
                memento.getContent();
    }
}
```

---

Important methods:

```java
save()

restore()
```

---

# Step 3: Caretaker

```java
public class History {

    private Stack<EditorMemento>
            history =
            new Stack<>();

    public void save(
            EditorMemento memento) {

        history.push(memento);
    }

    public EditorMemento undo() {

        return history.pop();
    }
}
```

---

Caretaker stores snapshots.

---

Never modifies them.

---

# Step 4: Client

```java
TextEditor editor =
        new TextEditor();

History history =
        new History();

editor.write("Hello");

history.save(
        editor.save());

editor.write(
        "Hello Java");

history.save(
        editor.save());

editor.write(
        "Hello Java Spring");

System.out.println(
        editor.getContent());
```

---

Output:

```text
Hello Java Spring
```

---

Undo:

```java
editor.restore(
        history.undo());

System.out.println(
        editor.getContent());
```

---

Output:

```text
Hello Java
```

---

# 9. Internal Working

Current state:

```text
Hello Java Spring
```

---

History Stack:

```text
Top
 ↓

Hello Java

Hello

Bottom
```

---

Undo:

```text
Pop Snapshot
```

---

Restore:

```text
Hello Java
```

---

# 10. Why Memento?

Without Memento:

```java
String oldState1;
String oldState2;
String oldState3;
```

---

With Memento:

```java
Stack<Memento>
```

---

Unlimited history support.

---

# 11. Undo Mechanism

Most common use case.

---

Flow:

```text
Before Change
      ↓
Save Snapshot
      ↓
Apply Change
```

---

Undo:

```text
Restore Snapshot
```

---

# 12. Redo Mechanism

Maintain:

```java
undoStack

redoStack
```

---

Undo:

```text
Move Snapshot
To Redo Stack
```

---

Redo:

```text
Restore Again
```

---

Used in:

```text
MS Word
VS Code
Photoshop
IntelliJ
```

---

# 13. Snapshot Pattern

Memento is often called:

```text
Snapshot Pattern
```

---

Because:

```text
State Captured
At Particular Time
```

---

Exactly like:

```text
Photo Snapshot
```

---

# 14. Deep Copy vs Shallow Copy

Most important interview topic.

---

Suppose:

```java
class Employee {

    String name;

    Address address;
}
```

---

# Shallow Copy

```java
Employee copy =
        original;
```

or

```java
clone()
```

default behavior.

---

Memory:

```text
Employee1
    ↓
 Address

Employee2
    ↓
 Address
```

---

Both share same address.

---

Bad for Memento.

---

# Deep Copy

Create completely separate objects.

---

Memory:

```text
Employee1
    ↓
 Address1

Employee2
    ↓
 Address2
```

---

Safe snapshot.

---

Recommended for Memento.

---

# 15. Complex Object Example

```java
class Employee {

    String name;

    Address address;

    Department department;
}
```

---

Memento should save:

```text
Name
Address
Department
```

---

Deeply.

---

Otherwise snapshot corruption occurs.

---

# 16. Serialization-Based Memento

Advanced interview topic.

---

Save object as:

```java
byte[]
```

using serialization.

---

Example:

```java
ObjectOutputStream
```

---

Restore:

```java
ObjectInputStream
```

---

Creates deep snapshot automatically.

---

Pros:

```text
Simple
```

---

Cons:

```text
Slow
Memory Heavy
```

---

# 17. Immutable Memento

Best practice.

---

Memento should be:

```java
final
```

and immutable.

---

Example:

```java
private final String state;
```

---

Reason:

```text
Snapshot Must Never Change
```

---

# 18. Java Examples

No direct Memento API exists.

---

But many tools implement the concept.

---

Examples:

```text
Undo Managers

IDE History

Version Control
```

---

# 19. Spring Framework Example

:contentReference[oaicite:0]{index=0}

Checkpoint mechanism resembles Memento.

---

Job state saved.

---

Later:

```text
Restart Job
```

from previous state.

---

# 20. Hibernate Example

Entity snapshots stored internally.

---

Used for:

```text
Dirty Checking
```

---

Hibernate compares:

```text
Old State

Current State
```

---

Conceptually similar.

---

# 21. Advantages

---

## 1. Supports Undo

Biggest benefit.

---

## 2. Preserves Encapsulation

External objects don't access internal fields directly.

---

## 3. Easy State Restoration

Restore previous snapshot.

---

## 4. Simplifies History Management

Caretaker handles history.

---

# 22. Disadvantages

---

## 1. High Memory Usage

Many snapshots.

---

## 2. Expensive Deep Copies

Large objects.

---

## 3. Complex For Huge Graphs

Large object trees.

---

# 23. Memento vs Command

Most asked interview question.

---

## Command

Stores:

```text
Action
```

---

Example:

```text
Delete File
Create Order
```

---

Undo by executing reverse action.

---

## Memento

Stores:

```text
State
```

---

Example:

```text
Editor Snapshot
```

---

### Memory Trick

```text
Command
      ↓
Store Action

Memento
      ↓
Store State
```

---

# 24. Memento vs Prototype

Another favorite interview question.

---

## Prototype

Creates object copy.

---

Purpose:

```text
New Object Creation
```

---

## Memento

Creates state snapshot.

---

Purpose:

```text
Undo / Restore
```

---

### Memory Trick

```text
Prototype
      ↓
Clone Object

Memento
      ↓
Save State
```

---

# 25. Memento vs Serialization

---

## Serialization

Persist object.

---

Example:

```text
Save To File
```

---

## Memento

Temporary state restoration.

---

Example:

```text
Undo
```

---

Different goals.

---

# 26. When To Use Memento

Use when:

- Undo needed
- Redo needed
- Snapshot history required
- Checkpoint system required

Examples:

```text
Text Editors

Games

Photoshop

IDEs

Workflow Engines
```

---

# 27. When NOT To Use

Avoid when:

- Object extremely large
- History unnecessary
- Memory constraints severe

---

# 28. Interview Questions

### Q1. What problem does Memento solve?

Stores and restores object state.

---

### Q2. What are participants?

```text
Originator

Memento

Caretaker
```

---

### Q3. What is Originator?

Object whose state is saved.

---

### Q4. What is Caretaker?

Stores mementos.

---

### Q5. Why should Memento be immutable?

To prevent snapshot corruption.

---

### Q6. Deep Copy or Shallow Copy?

```text
Deep Copy Preferred
```

---

### Q7. Difference Between Command and Memento?

Command stores action.

Memento stores state.

---

### Q8. Give real-world examples.

```text
Undo

Redo

Game Saves

Photoshop History

IDE History
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
Save And Restore State
```

---

# Quick Revision

```text
Memento Design Pattern
----------------------

Purpose:
Save and restore object state.

Participants:

1. Originator
2. Memento
3. Caretaker

Common Uses:

✔ Undo
✔ Redo
✔ Save Game
✔ Checkpoints
✔ History

Important Concepts:

✔ Snapshot
✔ Deep Copy
✔ Immutable Memento

Advantages:

✔ Easy Undo
✔ Preserves Encapsulation
✔ History Support

Disadvantages:

✘ High Memory Usage
✘ Expensive Snapshots

Remember:

Command
      ↓
Store Action

Memento
      ↓
Store State

Prototype
      ↓
Clone Object

Memento
      ↓
Save Object State
```