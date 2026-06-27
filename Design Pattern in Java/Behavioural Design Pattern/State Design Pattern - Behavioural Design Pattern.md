# State Design Pattern

> **Goal:** Understand State Design Pattern from scratch, why it exists, how it works internally, Context, State Interface, Concrete States, State Transition, Finite State Machine (FSM), real-world examples, Spring examples, advantages, disadvantages, interview questions, and how it differs from Strategy Pattern.

---

# 1. What Problem Does State Pattern Solve?

Suppose we are building a Vending Machine.

Possible states:

```text
No Coin State

Coin Inserted State

Product Dispensed State

Out Of Stock State
```

---

Without State Pattern:

```java
public class VendingMachine {

    private String state;

    public void insertCoin() {

        if(state.equals("NO_COIN")) {

            // insert coin
        }

        else if(state.equals("HAS_COIN")) {

            // already inserted
        }

        else if(state.equals("OUT_OF_STOCK")) {

            // reject
        }
    }

    public void dispense() {

        if(state.equals("NO_COIN")) {

            // error
        }

        else if(state.equals("HAS_COIN")) {

            // dispense
        }

        else if(state.equals("OUT_OF_STOCK")) {

            // reject
        }
    }
}
```

---

Problem:

As states grow:

```text
Too Many if-else
Hard To Maintain
Violates Open/Closed Principle
```

---

Need a way where:

```text
Each State Has Its Own Behavior
```

---

This is exactly what State Pattern solves.

---

# 2. Definition

> State Pattern allows an object to alter its behavior when its internal state changes. The object appears to change its class.

---

# 3. Simple Meaning

Instead of:

```text
One Giant Class
```

with many:

```java
if(state == ...)
```

---

Create:

```text
One Class Per State
```

---

Behavior automatically changes when state changes.

---

# 4. Real-Life Examples

---

## Example 1: Vending Machine

States:

```text
No Coin

Coin Inserted

Dispensing

Out Of Stock
```

---

Behavior changes based on state.

---

# Example 2: Traffic Signal

States:

```text
Red

Yellow

Green
```

---

Different behavior in each state.

---

# Example 3: ATM Machine

States:

```text
No Card

Card Inserted

Pin Verified

Transaction
```

---

# Example 4: Media Player

States:

```text
Playing

Paused

Stopped
```

---

Play button behaves differently.

---

# 5. Core Idea

Move behavior from:

```text
Huge if-else
```

to

```text
Individual State Classes
```

---

# 6. Components

---

## 1. Context

Main object.

---

Example:

```java
VendingMachine
```

---

## 2. State Interface

Common behavior.

---

Example:

```java
insertCoin()

dispense()
```

---

## 3. Concrete States

Actual states.

---

Example:

```text
NoCoinState

HasCoinState

DispensingState
```

---

# 7. UML Structure

```text
              State
                ↑

    ------------------------

    ↑          ↑         ↑

 NoCoin    HasCoin   Dispensing

                ↑

          VendingMachine
             (Context)
```

---

# 8. Real-World Example

# Vending Machine

---

## Step 1: State Interface

```java
public interface State {

    void insertCoin();

    void dispense();
}
```

---

# Step 2: Context

```java
public class VendingMachine {

    private State state;

    public void setState(
            State state) {

        this.state = state;
    }

    public State getState() {

        return state;
    }

    public void insertCoin() {

        state.insertCoin();
    }

    public void dispense() {

        state.dispense();
    }
}
```

---

Context delegates work to state.

---

# Step 3: NoCoinState

```java
public class NoCoinState
        implements State {

    private VendingMachine machine;

    public NoCoinState(
            VendingMachine machine) {

        this.machine = machine;
    }

    @Override
    public void insertCoin() {

        System.out.println(
                "Coin Inserted");

        machine.setState(
                new HasCoinState(
                        machine));
    }

    @Override
    public void dispense() {

        System.out.println(
                "Insert Coin First");
    }
}
```

---

# Step 4: HasCoinState

```java
public class HasCoinState
        implements State {

    private VendingMachine machine;

    public HasCoinState(
            VendingMachine machine) {

        this.machine = machine;
    }

    @Override
    public void insertCoin() {

        System.out.println(
                "Coin Already Present");
    }

    @Override
    public void dispense() {

        System.out.println(
                "Product Dispensed");

        machine.setState(
                new NoCoinState(
                        machine));
    }
}
```

---

# Step 5: Client

```java
VendingMachine machine =
        new VendingMachine();

machine.setState(
        new NoCoinState(
                machine));

machine.insertCoin();

machine.dispense();
```

---

Output:

```text
Coin Inserted

Product Dispensed
```

---

# 9. Internal Working

Initial:

```text
NoCoinState
```

---

Insert Coin:

```text
NoCoinState
      ↓
HasCoinState
```

---

Dispense:

```text
HasCoinState
      ↓
NoCoinState
```

---

Behavior changes automatically.

---

# 10. State Transition

Most important concept.

---

State Pattern is all about:

```text
State Transition
```

---

Example:

```text
Draft
 ↓
Review
 ↓
Approved
 ↓
Published
```

---

Every state has different behavior.

---

# 11. Finite State Machine (FSM)

Most important interview topic.

---

State Pattern is an OOP implementation of:

```text
Finite State Machine
```

---

Example:

```text
Red
 ↓
Green
 ↓
Yellow
 ↓
Red
```

---

Traffic signal.

---

FSM consists of:

```text
States

Transitions

Events
```

---

State Pattern models exactly this.

---

# 12. Traffic Signal Example

States:

```text
Red
Green
Yellow
```

---

Transitions:

```text
Red → Green

Green → Yellow

Yellow → Red
```

---

Classic FSM.

---

# 13. ATM Example

States:

```text
No Card

Card Inserted

Pin Verified

Transaction
```

---

Behavior differs per state.

---

Example:

```text
Withdraw Money
```

works only after:

```text
Pin Verified
```

---

Perfect State Pattern.

---

# 14. Workflow Example

Document states:

```text
Draft

Review

Approved

Published
```

---

Actions vary.

---

Example:

```text
Publish
```

not allowed from:

```text
Draft
```

---

Allowed from:

```text
Approved
```

---

# 15. Why Not Enum?

Common interview question.

---

Simple solution:

```java
enum State {
    DRAFT,
    REVIEW,
    APPROVED
}
```

---

Works for small systems.

---

Problem:

Behavior still requires:

```java
switch(state)
```

---

State Pattern eliminates that.

---

# 16. Java Examples

No direct State API.

---

But many frameworks use it.

---

Examples:

```text
Workflow Engines

State Machines

Game Engines
```

---

# 17. Spring State Machine

:contentReference[oaicite:0]{index=0}

Spring provides:

```text
State Machine Framework
```

---

Used for:

```text
Order Processing

Workflow

Approval Systems
```

---

Example:

```text
CREATED
 ↓
PAID
 ↓
SHIPPED
 ↓
DELIVERED
```

---

# 18. Order Processing Example

States:

```text
Created

Paid

Packed

Shipped

Delivered
```

---

Each state allows different operations.

---

Classic State Pattern.

---

# 19. Advantages

---

## 1. Eliminates Huge if-else

Biggest advantage.

---

## 2. Open/Closed Principle

Add new states easily.

---

## 3. Better Maintainability

Each state isolated.

---

## 4. Clear State Transitions

Easy to understand workflow.

---

## 5. Models FSM Naturally

Perfect for workflows.

---

# 20. Disadvantages

---

## 1. More Classes

Every state becomes a class.

---

## 2. Increased Complexity

For simple state systems.

---

## 3. State Transition Logic Spread

Can become difficult for large FSMs.

---

# 21. State vs Strategy

Most asked interview question.

---

They look almost identical.

---

## State Pattern

Object changes behavior automatically.

---

State transitions occur internally.

---

Example:

```text
ATM

No Card
 ↓
Card Inserted
 ↓
Transaction
```

---

Behavior depends on current state.

---

## Strategy Pattern

Client chooses algorithm.

---

Example:

```text
Credit Card Payment

UPI Payment

PayPal Payment
```

---

Client selects strategy.

---

### Memory Trick

```text
State
     ↓
Behavior Changes Automatically

Strategy
     ↓
Client Chooses Algorithm
```

---

# 22. State vs Enum

---

## Enum

```java
switch(state)
```

---

## State Pattern

```java
state.doAction()
```

---

No switch.

---

# 23. State vs Chain Of Responsibility

---

## State

Behavior depends on current state.

---

## CoR

Request passes through handlers.

---

Different goals.

---

# 24. When To Use State Pattern

Use when:

- Many states exist
- Behavior changes by state
- FSM required
- Workflow systems

Examples:

```text
ATM

Traffic Signal

Order Processing

Document Workflow

Media Player
```

---

# 25. When NOT To Use

Avoid when:

- Only 2–3 simple states
- State logic is tiny
- Enum sufficient

---

# 26. Interview Questions

### Q1. What problem does State Pattern solve?

Removes large if-else based on state.

---

### Q2. What are participants?

```text
Context

State

Concrete States
```

---

### Q3. What is Context?

Object whose behavior changes.

---

### Q4. What is FSM?

Finite State Machine.

---

### Q5. Why is State Pattern related to FSM?

Each state becomes a class.

---

### Q6. Difference Between State and Strategy?

State:

```text
Automatic State Transition
```

Strategy:

```text
Client Chooses Algorithm
```

---

### Q7. Give Spring example.

```text
Spring StateMachine
```

---

### Q8. Give real-world examples.

```text
ATM

Traffic Signal

Vending Machine

Workflow System
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

State
     ↓
Change Behavior By State
```

---

# Quick Revision

```text
State Design Pattern
--------------------

Purpose:
Change behavior based on current state.

Participants:

1. Context
2. State Interface
3. Concrete States

Key Concept:

✔ State Transition

Related To:

✔ Finite State Machine (FSM)

Examples:

✔ ATM
✔ Vending Machine
✔ Traffic Signal
✔ Media Player
✔ Workflow Engine

Advantages:

✔ Removes if-else
✔ Open/Closed Principle
✔ Better Maintainability

Disadvantages:

✘ More Classes
✘ Increased Complexity

Remember:

State
      ↓
Behavior Changes Automatically

Strategy
      ↓
Client Chooses Algorithm
```