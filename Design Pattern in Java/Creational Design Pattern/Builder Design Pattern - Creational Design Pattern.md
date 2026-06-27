# Builder Design Pattern

> **Goal:** Understand Builder Design Pattern from scratch, why it exists, how it works internally, different builder implementations, real-world examples, Lombok Builder, Spring Boot usage, advantages, disadvantages, and interview questions.

---

# 1. What Problem Does Builder Pattern Solve?

Suppose you have an Employee class:

```java
public class Employee {

    private String name;
    private int age;
    private String email;
    private String phone;
    private String address;
    private String department;
}
```

Now let's create an object.

```java
Employee employee =
    new Employee(
        "Sumit",
        25,
        "sumit@gmail.com",
        "9999999999",
        "Bangalore",
        "Engineering"
    );
```

---

## Problem #1: Constructor Confusion

Looking at this code:

```java
new Employee(
    "Sumit",
    25,
    "sumit@gmail.com",
    "9999999999",
    "Bangalore",
    "Engineering"
);
```

Can you immediately identify:

```text
Which parameter is phone?
Which parameter is address?
Which parameter is department?
```

Difficult.

---

## Problem #2: Too Many Constructors

Suppose some fields are optional.

You may create:

```java
Employee(name)
```

or

```java
Employee(name, age)
```

or

```java
Employee(name, age, email)
```

or

```java
Employee(name, age, email, phone)
```

Soon you'll have:

```text
10+
Constructors
```

This is called:

```text
Telescoping Constructor Problem
```

---

## Problem #3: Object Becomes Hard To Read

```java
Employee emp =
    new Employee(
        "Sumit",
        25,
        null,
        null,
        "Bangalore",
        null
    );
```

Nobody understands what is happening.

---

# 2. Definition

> Builder Pattern constructs complex objects step-by-step and allows creation of different representations of the same object.

In simple words:

```text
Instead of passing everything
at once,

build object gradually.
```

---

# 3. Real-Life Example

## Ordering a Burger

When ordering a burger:

You don't say:

```text
Create Burger Object
```

Instead:

```text
Add Bread
Add Cheese
Add Patty
Add Sauce
Add Vegetables
```

Finally:

```text
Build Burger
```

This is Builder Pattern.

---

## Flow

```text
Burger Builder
      ↓
Add Bread
      ↓
Add Cheese
      ↓
Add Patty
      ↓
Build Burger
```

---

# 4. Core Idea

Instead of:

```java
new Employee(...)
```

Use:

```java
Employee.builder()
        .name("Sumit")
        .age(25)
        .email("sumit@gmail.com")
        .build();
```

---

# 5. Traditional Builder Implementation

---

## Step 1: Create Product Class

```java
public class Employee {

    private String name;
    private int age;
    private String email;
    private String phone;

    private Employee(EmployeeBuilder builder) {

        this.name = builder.name;
        this.age = builder.age;
        this.email = builder.email;
        this.phone = builder.phone;
    }

}
```

---

## Step 2: Create Builder Class

```java
public static class EmployeeBuilder {

    private String name;
    private int age;
    private String email;
    private String phone;

    public EmployeeBuilder name(String name) {
        this.name = name;
        return this;
    }

    public EmployeeBuilder age(int age) {
        this.age = age;
        return this;
    }

    public EmployeeBuilder email(String email) {
        this.email = email;
        return this;
    }

    public EmployeeBuilder phone(String phone) {
        this.phone = phone;
        return this;
    }

    public Employee build() {
        return new Employee(this);
    }
}
```

---

## Step 3: Create Builder Method

Inside Employee:

```java
public static EmployeeBuilder builder() {
    return new EmployeeBuilder();
}
```

---

## Step 4: Client Code

```java
Employee employee =
        Employee.builder()
                .name("Sumit")
                .age(25)
                .email("sumit@gmail.com")
                .phone("9999999999")
                .build();
```

---

# 6. Internal Working

When this executes:

```java
Employee.builder()
        .name("Sumit")
        .age(25)
        .build();
```

Flow:

```text
EmployeeBuilder Created
      ↓
name() stores value
      ↓
age() stores value
      ↓
build()
      ↓
Employee Object Created
```

---

# 7. Why return "this" ?

Example:

```java
public EmployeeBuilder age(int age) {

    this.age = age;
    return this;
}
```

---

Without:

```java
return this;
```

method chaining won't work.

---

With:

```java
return this;
```

we can write:

```java
builder.name()
       .age()
       .email()
       .build();
```

---

This is called:

```text
Method Chaining
```

---

# 8. Builder vs Constructor

---

## Constructor

```java
Employee employee =
new Employee(
    "Sumit",
    25,
    "abc@gmail.com",
    "9999999999"
);
```

---

## Builder

```java
Employee employee =
Employee.builder()
        .name("Sumit")
        .age(25)
        .email("abc@gmail.com")
        .phone("9999999999")
        .build();
```

---

Builder is:

```text
More Readable
More Maintainable
```

---

# 9. Real-World Example: Car Builder

Suppose Car contains:

```text
Engine
Color
Seats
Sunroof
GPS
```

---

Without Builder:

```java
Car car =
new Car(
    "V8",
    "Black",
    5,
    true,
    true
);
```

---

With Builder:

```java
Car car =
Car.builder()
   .engine("V8")
   .color("Black")
   .seats(5)
   .sunroof(true)
   .gps(true)
   .build();
```

---

Immediately understandable.

---

# 10. Director Class (Original GoF Builder)

Many developers never use this in Java projects.

But it exists in the original Builder Pattern.

---

## Structure

```text
Director
    ↓
Builder
    ↓
Product
```

---

### Example

Director:

```java
public class CarDirector {

    public Car buildSportsCar(
            CarBuilder builder) {

        return builder
                .engine("V8")
                .seats(2)
                .sunroof(true)
                .build();
    }
}
```

---

Client:

```java
CarDirector director =
        new CarDirector();

Car car =
        director.buildSportsCar(
                new CarBuilder());
```

---

Director knows:

```text
Construction Process
```

Builder knows:

```text
Construction Details
```

---

# 11. Immutable Objects + Builder

Builder is heavily used for:

```text
Immutable Classes
```

---

Example:

```java
public final class Employee {

    private final String name;
    private final int age;
}
```

No setters.

Object can't change.

---

Builder creates object once.

After creation:

```text
Read Only
```

---

# 12. Lombok Builder

Most common in Spring Boot projects.

---

Instead of writing:

```java
EmployeeBuilder
```

manually,

use:

```java
@Builder
```

---

Example:

```java
import lombok.Builder;

@Builder
public class Employee {

    private String name;
    private int age;
    private String email;
}
```

---

Usage:

```java
Employee employee =
Employee.builder()
        .name("Sumit")
        .age(25)
        .email("sumit@gmail.com")
        .build();
```

---

Lombok automatically generates:

```text
Builder Class
Builder Methods
build()
builder()
```

---

# 13. Builder Pattern In Spring Boot

Builder is used everywhere.

---

## ResponseEntity

```java
ResponseEntity
        .ok()
        .header("token", "123")
        .body(data);
```

Builder-like design.

---

## UriComponentsBuilder

```java
UriComponentsBuilder
        .fromUriString(url)
        .queryParam("id", 1)
        .build();
```

---

## HttpSecurity

```java
http
    .csrf()
    .disable()
    .authorizeHttpRequests()
    .anyRequest()
    .authenticated();
```

Builder-style API.

---

# 14. Advantages

---

## 1. Readable Code

```java
.name("Sumit")
.age(25)
```

easy to understand.

---

## 2. Avoids Telescoping Constructors

No need for:

```java
10 Constructors
```

---

## 3. Supports Optional Fields

Provide only required values.

```java
Employee.builder()
        .name("Sumit")
        .build();
```

---

## 4. Immutable Object Creation

Perfect for immutable classes.

---

## 5. Method Chaining

Clean syntax.

---

## 6. Better Maintainability

Adding new field:

```java
salary
```

requires minimal changes.

---

# 15. Disadvantages

---

## More Code

Without Lombok:

```java
Builder Class
Methods
build()
```

must be written.

---

## Extra Object Creation

Builder object created before final object.

---

## Overkill For Small Classes

For:

```java
Student(id, name)
```

Builder may be unnecessary.

---

# 16. Builder vs Factory

This is a very common interview question.

---

## Factory

Focus:

```text
What Object To Create
```

Example:

```java
VehicleFactory.getVehicle("CAR");
```

---

## Builder

Focus:

```text
How To Build Object
```

Example:

```java
Car.builder()
   .engine("V8")
   .color("Black")
   .build();
```

---

# 17. Builder vs Abstract Factory

---

## Builder

Creates:

```text
One Complex Object
```

Example:

```text
One Car
```

---

## Abstract Factory

Creates:

```text
Family Of Objects
```

Example:

```text
Car
Seat
Engine
```

---

# 18. When To Use Builder Pattern

Use Builder when:

- Many constructor parameters exist
- Many optional fields exist
- Object creation is complex
- Immutable objects are required
- Readability is important

Examples:

```text
Employee
Car
House
API Request Objects
DTO Objects
Configuration Objects
```

---

# 19. When NOT To Use Builder

Avoid when:

```java
Student(id, name)
```

Only 2-3 fields exist.

Simple constructor is enough.

---

# 20. Interview Questions

### Q1. What problem does Builder Pattern solve?

It solves:

```text
Telescoping Constructor Problem
```

and improves readability.

---

### Q2. Why return "this" in Builder methods?

To support:

```text
Method Chaining
```

---

### Q3. Is Builder Pattern immutable?

Builder itself isn't immutable.

But it is commonly used to create immutable objects.

---

### Q4. What is Lombok Builder?

```java
@Builder
```

automatically generates Builder Pattern code.

---

### Q5. Difference Between Factory and Builder?

Factory:

```text
What To Create
```

Builder:

```text
How To Create
```

---

### Q6. What is Director in Builder Pattern?

Director controls:

```text
Construction Process
```

while Builder performs construction.

---

# Real-Life Memory Trick

```text
Factory
    ↓
Creates Product

Abstract Factory
    ↓
Creates Product Family

Builder
    ↓
Builds Complex Product Step By Step

Singleton
    ↓
Only One Product
```

---

# Quick Revision

```text
Builder Design Pattern
----------------------

Purpose:
Build complex objects step by step.

Problems Solved:

✔ Telescoping Constructors
✔ Poor Readability
✔ Optional Fields
✔ Immutable Object Creation

Structure:

Client
   ↓
Builder
   ↓
Product

Important Concepts:

1. Method Chaining
2. build()
3. Immutable Objects
4. Lombok @Builder
5. Director (GoF Version)

Advantages:

✔ Readable
✔ Flexible
✔ Maintainable
✔ Supports Optional Fields

Disadvantages:

✘ More Classes
✘ More Code

Remember:

Factory
   ↓
What Object?

Builder
   ↓
How To Build Object?
```