# Creational Design Patterns

> **Goal:** Understand what Creational Design Patterns are, why they exist, and when to use them.

---

# 1. What Problem Are Creational Design Patterns Solving?

Before learning the patterns, let's understand the problem.

Imagine you have a class:

```java
class Employee {
}
```

You create an object using:

```java
Employee emp = new Employee();
```

Simple, right?

But in real-world applications:

- Object creation can be complex.
- Some objects require many configurations.
- Some objects should be created only once.
- Sometimes we don't know which object to create until runtime.
- Sometimes creating an object is expensive.

If every developer creates objects differently, the code becomes:

- Hard to maintain
- Hard to extend
- Hard to test

To solve these object creation problems, **Creational Design Patterns** were introduced.

---

# 2. Definition

**Creational Design Patterns provide different ways to create objects while hiding the object creation logic from the client.**

Instead of writing:

```java
new Object()
```

everywhere, these patterns provide controlled ways of creating objects.

---

# 3. Real-Life Analogy

Suppose you want a car.

### Without a Factory

You build the entire car yourself:

- Engine
- Wheels
- Seats
- Steering

Very difficult.

### With a Factory

You simply say:

```text
I want a BMW.
```

The factory creates everything and gives you the final car.

This is exactly what Creational Patterns do.

They hide the complexity of object creation.

---

# 4. Types of Creational Design Patterns

There are **5 Creational Design Patterns**.

```text
Creational Design Patterns
│
├── Singleton
├── Factory Method
├── Abstract Factory
├── Builder
└── Prototype
```

---

# 5. [[Singleton Design Pattern - Creational Design Pattern]]

## Purpose

Ensure only one object of a class exists in the entire application.

### Example

Think about:

- Database Connection
- Logger
- Configuration Manager

You don't want 100 database connections created accidentally.

You want:

```text
Only One Object
```

### Without Singleton

```java
Database db1 = new Database();
Database db2 = new Database();
Database db3 = new Database();
```

Three objects created.

Memory wasted.

### With Singleton

```java
Database db1 = Database.getInstance();
Database db2 = Database.getInstance();
Database db3 = Database.getInstance();
```

Only one object exists.

### Real World Example

Spring Beans are singleton by default.

```java
@Service
public class EmployeeService {
}
```

Spring creates only one object.

### When To Use

Use Singleton when:

- Only one object should exist.
- Shared resource management is needed.

Examples:

- Logger
- Cache Manager
- Configuration Manager
- Database Connection Pool

---

# 6. [[Factory Method Pattern - Creational Design Pattern]]

## Purpose

Create objects without exposing object creation logic.

### Problem

Suppose you have:

```java
Car car = new BMW();
```

Later requirement changes:

```java
Car car = new Audi();
```

Then:

```java
Car car = new Mercedes();
```

Code changes everywhere.

### Solution

Use a Factory.

Client says:

```java
Car car = CarFactory.getCar("BMW");
```

Factory decides:

```java
if(type.equals("BMW"))
    return new BMW();

if(type.equals("AUDI"))
    return new Audi();
```

### Real-Life Example

Restaurant.

You order:

```text
Pizza
```

You don't make it yourself.

Kitchen (Factory) prepares it.

### Benefits

- Client doesn't know object creation details.
- Loose coupling.
- Easy to add new classes.

### Spring Example

```java
BeanFactory
```

is a Factory Pattern.

Spring creates beans for you.

---

# 7. [[Abstract Factory Pattern - Creational Design Pattern]]

## Purpose

Create families of related objects.

### First Understand Factory

Factory creates:

```text
One Product
```

Example:

```java
CarFactory
```

creates:

```text
BMW
Audi
Mercedes
```

### Abstract Factory Creates

```text
Multiple Related Products
```

Example:

```text
Luxury Car Factory
```

creates:

- Luxury Car
- Luxury Seat
- Luxury Engine

```text
Economy Car Factory
```

creates:

- Economy Car
- Economy Seat
- Economy Engine

### Real-Life Example

Furniture Store.

Modern Furniture Factory creates:

```text
Modern Chair
Modern Table
Modern Sofa
```

Victorian Furniture Factory creates:

```text
Victorian Chair
Victorian Table
Victorian Sofa
```

### Difference Between Factory and Abstract Factory

| Factory | Abstract Factory |
|----------|----------|
| Creates one product | Creates family of products |
| Simpler | More complex |
| One object creation | Multiple related object creation |

---

# 8.  [[Builder Design Pattern - Creational Design Pattern]]

## Purpose

Create complex objects step by step.

### Problem

Imagine Employee class:

```java
class Employee {

    String name;
    int age;
    String email;
    String phone;
    String address;
    String department;
}
```

Constructor:

```java
Employee e =
new Employee(
    "Sumit",
    25,
    "abc@gmail.com",
    "999999999",
    "Bangalore",
    "IT"
);
```

Looks confusing.

Nobody knows which parameter belongs to what.

### Builder Solution

```java
Employee e =
Employee.builder()
        .name("Sumit")
        .age(25)
        .email("abc@gmail.com")
        .department("IT")
        .build();
```

Much cleaner.

### Real-Life Example

Ordering Burger.

You choose:

```text
Bread
Cheese
Sauce
Patty
```

Step by step.

Finally burger is built.

### Advantages

- Readable code
- Avoid huge constructors
- Optional fields supported

### Spring Example

```java
ResponseEntity
    .ok()
    .header("token","123")
    .body(data);
```

Builder-like style.

---

# 9.  [[Prototype Design Pattern - Creational Design Pattern]]

## Purpose

Create new objects by copying existing objects.

### Problem

Suppose creating an object is expensive.

```java
Employee e1 = new Employee();
```

It loads:

- Database data
- Configuration
- External API data

Takes 5 seconds.

Creating another object again takes 5 seconds.

### Solution

Clone existing object.

```java
Employee e2 = e1.clone();
```

Object copied instantly.

### Real-Life Example

Photocopy Machine.

Instead of writing notes again:

```text
Original Document
       ↓
Photocopy
```

Same concept.

### Java Support

Java provides:

```java
Cloneable
```

interface.

```java
class Employee implements Cloneable
```

### Advantages

- Fast object creation
- Useful for expensive objects

---

# 10. Quick Revision Table

| Pattern | Main Purpose |
|----------|-------------|
| Singleton | Only one object |
| Factory Method | Hide object creation |
| Abstract Factory | Create family of objects |
| Builder | Create complex object step-by-step |
| Prototype | Clone existing object |

---

# 11. How To Identify Which Pattern To Use

### Need only one object?

Use:

```text
Singleton
```

### Need to hide object creation?

Use:

```text
Factory Method
```

### Need related groups of objects?

Use:

```text
Abstract Factory
```

### Need object with many fields?

Use:

```text
Builder
```

### Object creation expensive?

Use:

```text
Prototype
```

---

# Interview Questions

### Q1. What are Creational Design Patterns?

**Answer:**

Creational Design Patterns deal with object creation mechanisms and provide flexible ways to create objects while hiding creation logic.

---

### Q2. Name all Creational Design Patterns.

1. Singleton
2. Factory Method
3. Abstract Factory
4. Builder
5. Prototype

---

### Q3. Which Creational Pattern is used most in Spring Boot?

Common answers:

- Singleton
- Factory Method
- Builder

---

### Q4. What is the difference between Factory and Builder?

| Factory | Builder |
|----------|----------|
| Creates object in one step | Creates object step-by-step |
| Focuses on which object to create | Focuses on how to create object |
| Returns ready object | Gradually builds object |

---

# Notes Summary

```text
Creational Design Patterns
--------------------------
Purpose:
Control and simplify object creation.

Patterns:
1. Singleton      -> One object only
2. Factory Method -> Hide object creation
3. Abstract Factory -> Family of related objects
4. Builder -> Complex object creation
5. Prototype -> Clone existing object

Remember:

Singleton = One
Factory = Create
Abstract Factory = Family
Builder = Construct
Prototype = Copy
```

# Structural Design Patterns

> **Goal:** Understand what Structural Design Patterns are, why they are needed, and when to use them.

---

# 1. What Problem Are Structural Design Patterns Solving?

After creating objects, the next challenge is:

```text
How should these objects interact with each other?
```

In real-world applications:

- Different classes need to communicate.
- Existing classes may have incompatible interfaces.
- Complex systems may contain hundreds of classes.
- We may need to add functionality without modifying existing code.
- We may want to reduce memory consumption.

Without proper structure, code becomes:

- Tightly coupled
- Hard to maintain
- Difficult to extend
- Difficult to test

To solve these problems, **Structural Design Patterns** were introduced.

---

# 2. Definition

**Structural Design Patterns focus on how classes and objects are composed to form larger structures while keeping those structures flexible and efficient.**

In simple words:

```text
Creational Patterns  -> How to create objects
Structural Patterns  -> How to connect objects
Behavioral Patterns  -> How objects communicate
```

---

# 3. Real-Life Analogy

Think about building a house.

Creational Patterns help create:

- Bricks
- Doors
- Windows

Structural Patterns help organize them into:

- Rooms
- Floors
- Complete House

The focus is not on creating the components but on arranging them properly.

---

# 4. Types of Structural Design Patterns

There are **7 Structural Design Patterns**.

```text
Structural Design Patterns
│
├── Adapter
├── Bridge
├── Composite
├── Decorator
├── Facade
├── Flyweight
└── Proxy
```

---

# 5. [[Adapter Design Pattern - Structural Design Pattern]]

## Purpose

Convert one interface into another interface expected by the client.

---

## Problem

Suppose your mobile charger supports:

```text
USB-C
```

But your old phone supports:

```text
Micro USB
```

They cannot connect directly.

---

## Solution

Use an Adapter.

```text
USB-C Charger
      ↓
    Adapter
      ↓
Micro USB Phone
```

The adapter converts one interface into another.

---

## Software Example

Suppose your application expects:

```java
interface PaymentProcessor {
    void pay();
}
```

But a third-party library provides:

```java
class RazorPay {
    void makePayment() {
    }
}
```

Interfaces don't match.

Adapter converts:

```java
pay()
```

into:

```java
makePayment()
```

---

## Real-Life Example

- Mobile charger adapter
- Power socket converter
- USB to Ethernet adapter

---

## Benefits

- Reuse existing code
- Integrate third-party libraries
- Avoid changing old code

---

# 6. [[Bridge Design Pattern - Structural Design Pattern]]

## Purpose

Separate abstraction from implementation so both can change independently.

---

## Problem

Suppose you have:

```text
Shape
│
├── Circle
├── Rectangle
```

Now colors are introduced:

```text
Red Circle
Blue Circle
Red Rectangle
Blue Rectangle
```

Classes start increasing rapidly.

---

## Without Bridge

```text
Circle
RedCircle
BlueCircle

Rectangle
RedRectangle
BlueRectangle
```

This becomes difficult to manage.

---

## Solution

Separate:

```text
Shape
```

from

```text
Color
```

Now:

```text
Shape -------> Color
```

A Circle can use any Color.

A Rectangle can use any Color.

---

## Real-Life Example

TV Remote.

```text
Remote (Abstraction)
```

can work with

```text
Sony TV
Samsung TV
LG TV
```

Implementation changes independently.

---

## Benefits

- Reduces class explosion
- Easy maintenance
- Better flexibility

---

# 7. [[Composite Design Pattern - Structural Design Pattern]]

## Purpose

Treat individual objects and groups of objects in the same way.

---

## Problem

Suppose a company has:

```text
CEO
 ├── Manager
 │    ├── Developer
 │    ├── Developer
 │
 ├── Manager
      ├── Tester
      ├── Tester
```

Both individual employees and groups need to be processed similarly.

---

## Solution

Create a common interface.

```java
interface Employee {
    void showDetails();
}
```

Now:

```text
Developer
Tester
Manager
Department
```

all implement the same interface.

---

## Real-Life Example

File System.

```text
Folder
 ├── Folder
 ├── Folder
 └── File
```

Whether it's a file or folder, users interact similarly.

---

## Benefits

- Uniform treatment
- Recursive tree structures
- Simplifies client code

---

# 8. [[Decorator Design Pattern - Structural Design Pattern]]

## Purpose

Add new functionality to an object dynamically without modifying its code.

---

## Problem

Suppose you have:

```java
Coffee coffee = new BasicCoffee();
```

Customer wants:

```text
Coffee + Milk
```

Another wants:

```text
Coffee + Milk + Chocolate
```

Another wants:

```text
Coffee + Milk + Chocolate + Caramel
```

Creating separate classes for each combination becomes impossible.

---

## Solution

Wrap objects using decorators.

```text
Basic Coffee
      ↓
Milk Decorator
      ↓
Chocolate Decorator
      ↓
Caramel Decorator
```

Each decorator adds functionality.

---

## Real-Life Example

Pizza Toppings.

```text
Base Pizza
```

Add:

```text
Cheese
Olives
Mushrooms
```

without changing original pizza.

---

## Benefits

- Open/Closed Principle
- Dynamic functionality
- Flexible design

---

# 9. [[Facade Design Pattern - Structural Design Pattern]]

## Purpose

Provide a simplified interface to a complex subsystem.

---

## Problem

Suppose watching a movie requires:

```text
Turn on TV
Turn on Sound System
Turn on Projector
Set Input Source
Adjust Volume
```

Too many steps.

---

## Solution

Create a Facade.

```java
homeTheater.watchMovie();
```

Facade internally performs:

```text
TV ON
Projector ON
Speaker ON
Volume Setup
```

---

## Software Example

Suppose placing an order requires:

```java
inventoryService.checkStock();
paymentService.processPayment();
shippingService.createShipment();
notificationService.sendEmail();
```

Instead of calling all separately:

```java
orderFacade.placeOrder();
```

---

## Real-Life Example

ATM Machine.

Internally:

```text
Account Verification
Balance Check
Cash Processing
Transaction Logging
```

User only presses:

```text
Withdraw Money
```

---

## Benefits

- Simplifies usage
- Hides complexity
- Reduces dependencies

---

# 10. [[Flyweight Design Pattern - Structural Design Pattern]] 

## Purpose

Reduce memory usage by sharing common objects.

---

## Problem

Suppose a text editor contains:

```text
100000 characters
```

Each character stores:

```text
Font
Size
Color
Position
```

Huge memory consumption.

---

## Solution

Share common data.

Instead of:

```text
100000 font objects
```

Store:

```text
1 font object
```

and reuse it.

---

## Real-Life Example

Chess Game.

Instead of creating:

```text
1000 White Pawn Objects
```

share common data.

---

## Benefits

- Saves memory
- Improves performance
- Useful for large object collections

---

# 11. [[Proxy Design Pattern - Structural Design Pattern]]

## Purpose

Control access to another object.

---

## Problem

Suppose accessing an object is:

- Expensive
- Sensitive
- Remote

You don't want direct access.

---

## Solution

Use a Proxy.

```text
Client
   ↓
 Proxy
   ↓
 Real Object
```

Proxy decides:

```text
Allow?
Deny?
Load?
Cache?
```

---

## Types of Proxy

### Virtual Proxy

Lazy Loading.

```java
Image image = new ProxyImage();
```

Actual image loads only when needed.

---

### Security Proxy

Checks permissions.

```java
if(user.isAdmin())
```

allow access.

---

### Remote Proxy

Access object on another server.

---

## Real-Life Example

Bank Locker.

```text
Customer
    ↓
Security Guard
    ↓
Locker
```

Guard controls access.

---

## Spring Example

Spring AOP uses Proxy Pattern.

```java
@Transactional
```

creates proxies behind the scenes.

---

## Benefits

- Security
- Lazy loading
- Access control
- Performance optimization

---

# 12. Quick Revision Table

| Pattern | Main Purpose |
|----------|-------------|
| Adapter | Convert one interface into another |
| Bridge | Separate abstraction from implementation |
| Composite | Treat groups and individuals uniformly |
| Decorator | Add functionality dynamically |
| Facade | Simplify complex systems |
| Flyweight | Share objects to save memory |
| Proxy | Control access to objects |

---

# 13. How To Identify Which Pattern To Use

### Need interface conversion?

Use:

```text
Adapter
```

---

### Need abstraction and implementation separated?

Use:

```text
Bridge
```

---

### Need tree structure?

Use:

```text
Composite
```

---

### Need dynamic functionality?

Use:

```text
Decorator
```

---

### Need simplified interface?

Use:

```text
Facade
```

---

### Need memory optimization?

Use:

```text
Flyweight
```

---

### Need controlled access?

Use:

```text
Proxy
```

---

# Interview Questions

### Q1. What are Structural Design Patterns?

**Answer:**

Structural Design Patterns focus on how classes and objects are composed to form larger structures while keeping them flexible and efficient.

---

### Q2. Name all Structural Design Patterns.

1. Adapter
2. Bridge
3. Composite
4. Decorator
5. Facade
6. Flyweight
7. Proxy

---

### Q3. What is the difference between Adapter and Facade?

| Adapter | Facade |
|----------|----------|
| Converts interfaces | Simplifies interfaces |
| Makes incompatible classes work together | Hides complexity |
| Focuses on compatibility | Focuses on usability |

---

### Q4. What is the difference between Decorator and Proxy?

| Decorator | Proxy |
|------------|--------|
| Adds functionality | Controls access |
| Enhances behavior | Protects behavior |
| Focus on extension | Focus on control |

---

### Q5. Which Structural Pattern is heavily used in Spring?

Common answers:

- Proxy (Spring AOP)
- Decorator (Servlet Wrappers)
- Facade (JdbcTemplate, RestTemplate abstractions)

---

# Notes Summary

```text
Structural Design Patterns
--------------------------
Purpose:
Organize classes and objects into larger structures.

Patterns:

1. Adapter    -> Convert Interface
2. Bridge     -> Separate Abstraction & Implementation
3. Composite  -> Tree Structure
4. Decorator  -> Add Features
5. Facade     -> Simplify Complex Systems
6. Flyweight  -> Save Memory
7. Proxy      -> Control Access

Remember:

Adapter   = Convert
Bridge    = Separate
Composite = Group
Decorator = Enhance
Facade    = Simplify
Flyweight = Share
Proxy     = Control
```

# Behavioral Design Patterns

> **Goal:** Understand what Behavioral Design Patterns are, why they exist, and when to use them.

---

# 1. What Problem Are Behavioral Design Patterns Solving?

After creating objects and connecting them together, another problem appears:

```text
How should these objects communicate with each other?
```

In real-world applications:

- Objects need to exchange information.
- Different objects may perform different actions for the same request.
- Some objects need to react when another object changes.
- Some workflows need fixed steps but flexible implementations.
- Some objects need to maintain and restore previous states.

Without proper communication mechanisms, code becomes:

- Tightly coupled
- Difficult to modify
- Hard to extend
- Difficult to test

To solve these communication problems, **Behavioral Design Patterns** were introduced.

---

# 2. Definition

**Behavioral Design Patterns focus on communication and interaction between objects.**

In simple words:

```text
Creational Patterns  -> How objects are created
Structural Patterns  -> How objects are connected
Behavioral Patterns  -> How objects communicate
```

---

# 3. Real-Life Analogy

Think about a company.

Creating employees:

```text
Creational Patterns
```

Organizing departments:

```text
Structural Patterns
```

Communication between employees:

```text
Behavioral Patterns
```

Behavioral patterns define:

- Who talks to whom
- How requests are handled
- How responsibilities are distributed

---

# 4. Types of Behavioral Design Patterns

There are **11 Behavioral Design Patterns**.

```text
Behavioral Design Patterns
│
├── Chain of Responsibility
├── Command
├── Interpreter
├── Iterator
├── Mediator
├── Memento
├── Observer
├── State
├── Strategy
├── Template Method
└── Visitor
```

---

# 5. [[Chain of Responsibility (CoR) Design Pattern - Behavioural Design Pattern]]

## Purpose

Pass a request through a chain of handlers until someone handles it.

---

## Problem

Suppose leave approval process is:

```text
Employee
    ↓
Team Lead
    ↓
Manager
    ↓
Director
```

Who should approve?

Depends on the leave duration.

---

## Solution

Pass request along the chain.

```text
Employee Request
      ↓
Team Lead
      ↓
Manager
      ↓
Director
```

Each handler decides:

```text
Can I handle this?
```

If yes:

```text
Handle Request
```

Otherwise:

```text
Pass to Next Handler
```

---

## Real-Life Example

Customer Care Escalation.

```text
Level 1 Support
      ↓
Level 2 Support
      ↓
Manager
```

---

## Spring Example

Spring Security Filter Chain.

```java
AuthenticationFilter
AuthorizationFilter
JWTFilter
```

Each filter processes the request.

---

## Benefits

- Loose coupling
- Flexible request handling
- Easy to add handlers

---

# 6. [[Command Design Pattern - Behavioural Design Pattern]]

## Purpose

Encapsulate a request as an object.

---

## Problem

Suppose a remote control can:

```text
Turn On TV
Turn Off TV
Increase Volume
Decrease Volume
```

Without Command Pattern, remote becomes tightly coupled with TV.

---

## Solution

Convert actions into objects.

```text
TurnOnCommand
TurnOffCommand
VolumeUpCommand
```

Remote executes commands without knowing details.

---

## Real-Life Example

Restaurant Order.

```text
Customer
     ↓
Order Slip (Command)
     ↓
Chef
```

Chef doesn't interact with customer directly.

---

## Common Use Cases

- Undo/Redo
- Queue Processing
- Task Scheduling

---

## Benefits

- Decouples sender and receiver
- Supports Undo/Redo
- Supports Logging

---

# 7. [[Interpreter Design Pattern - Behavioural Design Pattern]]

## Purpose

Define grammar and interpret expressions.

---

## Problem

Need to evaluate expressions like:

```text
2 + 3
```

or

```text
A AND B
```

---

## Solution

Represent grammar as classes.

Example:

```text
Expression
    ↓
NumberExpression
AddExpression
MultiplyExpression
```

Each class interprets part of the expression.

---

## Real-Life Example

SQL Parser

```sql
SELECT * FROM Employee
```

Interpreter understands SQL grammar.

---

## Benefits

- Easy grammar implementation
- Extensible language processing

---

## Drawback

Can become complex for large grammars.

---

# 8. [[Iterator Design Pattern - Behavioural Design Pattern]]

## Purpose

Traverse collections without exposing internal structure.

---

## Problem

Suppose you have:

```java
List<Employee>
Set<Employee>
Queue<Employee>
```

Each collection stores data differently.

---

## Solution

Provide a common traversal mechanism.

```java
Iterator<Employee> iterator =
employees.iterator();
```

---

## Example

```java
while(iterator.hasNext()) {
    System.out.println(iterator.next());
}
```

---

## Real-Life Example

TV Remote.

You don't know how channels are stored.

You simply press:

```text
Next
```

---

## Java Example

```java
Iterator
ListIterator
```

---

## Benefits

- Uniform traversal
- Hides internal structure

---

# 9. [[Mediator Design Pattern - Behavioural Design Pattern]]

## Purpose

Centralize communication between objects.

---

## Problem

Suppose multiple objects communicate directly.

```text
A ↔ B
A ↔ C
A ↔ D
B ↔ C
B ↔ D
C ↔ D
```

Too many connections.

---

## Solution

Introduce a Mediator.

```text
      Mediator
      /  |  \
     A   B   C
```

Objects talk only to mediator.

---

## Real-Life Example

Air Traffic Control.

```text
Plane A
Plane B
Plane C
     ↓
Air Traffic Controller
```

Planes don't communicate directly.

---

## Benefits

- Reduced dependencies
- Centralized communication
- Easier maintenance

---

# 10. [[Memento Design Pattern - Behavioural Design Pattern]]

## Purpose

Capture and restore object state.

---

## Problem

Need Undo functionality.

Example:

```text
Document Version 1
Document Version 2
Document Version 3
```

User clicks:

```text
Undo
```

---

## Solution

Store snapshots.

```text
State 1
State 2
State 3
```

Restore previous state when needed.

---

## Real-Life Example

Game Save Feature.

```text
Save Game
Load Game
```

---

## Benefits

- Undo functionality
- State restoration
- History tracking

---

# 11. [[Observer Design Pattern - Behavioural Design Pattern]]

## Purpose

Automatically notify dependent objects when an object changes.

---

## Problem

Suppose YouTube uploads a video.

Need to notify:

```text
Subscriber 1
Subscriber 2
Subscriber 3
```

---

## Solution

Create:

```text
Subject
```

and

```text
Observers
```

When subject changes:

```text
Notify All Observers
```

---

## Real-Life Example

Instagram Followers

```text
New Post
      ↓
Followers Notified
```

---

## Java Example

Event Listeners

```java
ActionListener
MouseListener
```

---

## Benefits

- Loose coupling
- Event-driven systems
- Automatic updates

---

# 12. [[State Design Pattern - Behavioural Design Pattern]]

## Purpose

Change object behavior when its internal state changes.

---

## Problem

Suppose a vending machine has states:

```text
No Coin
Coin Inserted
Product Dispensed
```

Behavior changes based on current state.

---

## Solution

Create separate classes for each state.

```text
NoCoinState
CoinInsertedState
DispenseState
```

Object delegates behavior to current state.

---

## Real-Life Example

Traffic Signal.

```text
Red
Yellow
Green
```

Each state behaves differently.

---

## Benefits

- Cleaner code
- Removes large if-else blocks
- Easy state management

---

# 13. Strategy Pattern

## Purpose

Encapsulate interchangeable algorithms.

---

## Problem

Suppose payment can be:

```text
Credit Card
UPI
PayPal
Net Banking
```

Using:

```java
if(paymentType.equals(...))
```

everywhere becomes messy.

---

## Solution

Create separate strategies.

```text
CreditCardStrategy
UPIStrategy
PaypalStrategy
```

Select one at runtime.

---

## Real-Life Example

Google Maps Route Selection.

```text
Car Route
Bike Route
Walking Route
```

Different algorithms.

---

## Benefits

- Easy to switch algorithms
- Open/Closed Principle
- Cleaner code

---

# 14. [[Template Method Design Pattern - Behavioural Design Pattern]]

## Purpose

Define an algorithm skeleton while allowing subclasses to implement specific steps.

---

## Problem

Suppose report generation process is:

```text
Load Data
Process Data
Generate Report
Send Report
```

Most steps remain same.

Only report generation changes.

---

## Solution

Base class defines workflow.

```text
Step 1
Step 2
Step 3
Step 4
```

Subclasses customize certain steps.

---

## Real-Life Example

Making Tea vs Coffee.

Common steps:

```text
Boil Water
Pour Into Cup
```

Different steps:

```text
Add Tea
Add Coffee
```

---

## Spring Example

```java
JdbcTemplate
RestTemplate
```

---

## Benefits

- Code reuse
- Consistent workflow
- Easy customization

---

# 15. [[Visitor Design Pattern - Behavioural Design Pattern]]

## Purpose

Add new operations without modifying existing classes.

---

## Problem

Suppose classes already exist:

```text
Circle
Rectangle
Triangle
```

Need to add:

```text
Area Calculation
Drawing
Exporting
Printing
```

Modifying every class repeatedly is difficult.

---

## Solution

Create Visitors.

```text
AreaVisitor
DrawingVisitor
ExportVisitor
```

Visitor performs operations externally.

---

## Real-Life Example

Tax Calculation.

Different products:

```text
Book
Food
Electronics
```

Tax Visitor calculates taxes.

---

## Benefits

- Add operations easily
- Open/Closed Principle
- Separation of concerns

---

# 16. Quick Revision Table

| Pattern | Main Purpose |
|----------|-------------|
| Chain of Responsibility | Pass requests through handlers |
| Command | Encapsulate requests |
| Interpreter | Interpret grammar |
| Iterator | Traverse collections |
| Mediator | Centralize communication |
| Memento | Save and restore state |
| Observer | Notify dependent objects |
| State | Change behavior by state |
| Strategy | Swap algorithms |
| Template Method | Define workflow skeleton |
| Visitor | Add operations externally |

---

# 17. How To Identify Which Pattern To Use

### Need request processing chain?

Use:

```text
Chain of Responsibility
```

### Need Undo/Redo?

Use:

```text
Command
```

or

```text
Memento
```

### Need collection traversal?

Use:

```text
Iterator
```

### Need event notification?

Use:

```text
Observer
```

### Need behavior based on state?

Use:

```text
State
```

### Need multiple algorithms?

Use:

```text
Strategy
```

### Need fixed workflow?

Use:

```text
Template Method
```

### Need centralized communication?

Use:

```text
Mediator
```

---

# Interview Questions

### Q1. What are Behavioral Design Patterns?

**Answer:**

Behavioral Design Patterns focus on communication and interaction between objects and define how responsibilities are distributed among them.

---

### Q2. Name all Behavioral Design Patterns.

1. Chain of Responsibility
2. Command
3. Interpreter
4. Iterator
5. Mediator
6. Memento
7. Observer
8. State
9. Strategy
10. Template Method
11. Visitor

---

### Q3. What is the difference between Strategy and State?

| Strategy | State |
|-----------|--------|
| Client chooses algorithm | State changes automatically |
| Focus on behavior selection | Focus on state transition |
| Independent algorithms | State-dependent behavior |

---

### Q4. What is the difference between Command and Chain of Responsibility?

| Command | Chain of Responsibility |
|----------|------------------------|
| Encapsulates request | Passes request |
| One receiver executes | Multiple handlers may process |
| Used for actions | Used for request routing |

---

### Q5. Which Behavioral Patterns are commonly used in Spring?

Common answers:

- Observer (Events)
- Template Method (JdbcTemplate)
- Chain of Responsibility (Spring Security)
- Strategy (Authentication Providers)

---

# Notes Summary

```text
Behavioral Design Patterns
--------------------------
Purpose:
Manage communication between objects.

Patterns:

1. Chain of Responsibility -> Pass Request
2. Command                -> Encapsulate Action
3. Interpreter            -> Interpret Grammar
4. Iterator               -> Traverse Collection
5. Mediator               -> Centralize Communication
6. Memento                -> Save State
7. Observer               -> Notify Changes
8. State                  -> Change Behavior
9. Strategy               -> Swap Algorithms
10. Template Method       -> Define Workflow
11. Visitor               -> Add Operations

Remember:

Chain      = Route
Command    = Action
Interpreter= Grammar
Iterator   = Traverse
Mediator   = Centralize
Memento    = Save
Observer   = Notify
State      = Behavior Change
Strategy   = Algorithm
Template   = Workflow
Visitor    = Operation
```
