# Singleton Design Pattern

> **Goal:** Understand Singleton Design Pattern from scratch, why it exists, how it works internally, all implementation types, thread-safety issues, Spring Boot relation, interview questions, and real-world examples.

---

# 1. What Problem Does Singleton Solve?

Imagine a company application.

There is a class:

```java
class DatabaseConnection {
}
```

And developers create objects like:

```java
DatabaseConnection db1 = new DatabaseConnection();
DatabaseConnection db2 = new DatabaseConnection();
DatabaseConnection db3 = new DatabaseConnection();
```

Now suppose each object creates a database connection.

Result:

```text
Connection 1
Connection 2
Connection 3
Connection 4
Connection 5
...
```

This causes:

- Memory waste
- Resource waste
- Performance issues

But logically:

```text
Only ONE database connection manager should exist.
```

This is where Singleton comes in.

---

# 2. Definition

> Singleton Pattern ensures that only one object of a class exists in the JVM and provides a global access point to that object.

---

# 3. Real-Life Example

## CEO of a Company

A company can have:

```text
Many Employees
Many Managers
Many Teams
```

But only:

```text
One CEO
```

If somebody asks:

```text
Give me CEO
```

everyone gets the same CEO.

Not a new CEO.

Singleton works exactly the same way.

---

# 4. Characteristics of Singleton

A Singleton class must:

### 1. Have Private Constructor

```java
private Singleton() {
}
```

Why?

Prevent external object creation.

---

### 2. Have Static Instance Variable

```java
private static Singleton instance;
```

Why?

Because instance belongs to class, not object.

---

### 3. Provide Public Method

```java
public static Singleton getInstance()
```

Why?

To provide controlled access.

---

# 5. Basic Singleton Structure

```java
class Singleton {

    private static Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {

        if(instance == null) {
            instance = new Singleton();
        }

        return instance;
    }
}
```

Usage:

```java
Singleton s1 = Singleton.getInstance();
Singleton s2 = Singleton.getInstance();
```

Both references point to same object.

---

# 6. Verify Singleton

```java
System.out.println(s1.hashCode());
System.out.println(s2.hashCode());
```

Output:

```text
12345
12345
```

Same object.

---

# 7. Types of Singleton Implementations

There are several ways to implement Singleton.

```text
1. Eager Initialization
2. Lazy Initialization
3. Synchronized Method
4. Single Check + Synchronized Block (Wrong Approach)
5. Double Checked Locking
6. Bill Pugh Singleton
7. Enum Singleton
```

---

# 8. Eager Initialization Singleton

## Idea

Create object immediately when class loads.

---

### Code

```java
public class DatabaseManager {

    private static final DatabaseManager INSTANCE =
            new DatabaseManager();

    private DatabaseManager() {
    }

    public static DatabaseManager getInstance() {
        return INSTANCE;
    }
}
```

---

### Flow

When JVM loads class:

```text
Class Loading
      ↓
Object Created
      ↓
Ready To Use
```

---

### Advantages

- Thread Safe
- Simple

---

### Disadvantages

Object created even if never used.

Example:

```text
Application Started
      ↓
DatabaseManager Created
      ↓
Never Used
```

Memory wasted.

---

# 9. Lazy Initialization Singleton

## Idea

Create object only when required.

---

### Code

```java
public class DatabaseManager {

    private static DatabaseManager instance;

    private DatabaseManager() {
    }

    public static DatabaseManager getInstance() {

        if(instance == null) {
            instance = new DatabaseManager();
        }

        return instance;
    }
}
```

---

### Flow

```text
Application Started
      ↓
No Object
      ↓
First Request
      ↓
Object Created
```

---

### Advantage

Memory efficient.

---

### Problem

Not Thread Safe.

---

# 10. Thread Safety Problem

Suppose two threads enter simultaneously.

```text
Thread-1
Thread-2
```

Current instance:

```java
instance == null
```

---

Thread-1 checks:

```java
if(instance == null)
```

True.

Before creating object...

CPU switches.

---

Thread-2 checks:

```java
if(instance == null)
```

True.

Creates object.

---

CPU switches.

Thread-1 creates another object.

Result:

```text
Object 1
Object 2
```

Singleton broken.

---

# 11. Synchronized Method Singleton

## Solution

Lock entire method.

---

### Code

```java
public class DatabaseManager {

    private static DatabaseManager instance;

    private DatabaseManager() {
    }

    public static synchronized DatabaseManager getInstance() {

        if(instance == null) {
            instance = new DatabaseManager();
        }

        return instance;
    }
}
```

---

### Flow

```text
Thread-1 enters
      ↓
Method Locked
      ↓
Object Created
      ↓
Thread-1 exits
      ↓
Thread-2 enters
```

---

### Advantage

Thread Safe.

---

### Problem

Every call requires lock.

Even after object exists.

Performance decreases.

---

# 12. Single Check + Synchronized Block (Common Mistake)

Some developers write:

```java
public static DatabaseManager getInstance() {

    if(instance == null) {

        synchronized(DatabaseManager.class) {
            instance = new DatabaseManager();
        }
    }

    return instance;
}
```

Looks correct.

Actually not.

---

### Problem

Thread-1:

```java
if(instance == null)
```

True.

---

Thread-2:

```java
if(instance == null)
```

True.

---

Both enter synchronized block one after another.

Each creates object.

Singleton broken.

---

### Result

```text
Multiple Objects Created
```

Wrong approach.

---

# 13. Double Checked Locking Singleton

## Correct Solution

Check twice.

---

### Code

```java
public class DatabaseManager {

    private static volatile DatabaseManager instance;

    private DatabaseManager() {
    }

    public static DatabaseManager getInstance() {

        if(instance == null) {

            synchronized(DatabaseManager.class) {

                if(instance == null) {
                    instance = new DatabaseManager();
                }
            }
        }

        return instance;
    }
}
```

---

### Why Double Check?

First Check:

```java
if(instance == null)
```

Avoid unnecessary locking.

---

Second Check:

```java
if(instance == null)
```

Ensure another thread didn't already create object.

---

### Why volatile?

Without volatile:

JVM may reorder instructions.

Possible:

```text
Memory Allocated
Reference Assigned
Constructor Executes
```

Another thread sees half-created object.

---

### volatile prevents this.

---

### Advantages

- Thread Safe
- High Performance

---

### Most Popular Interview Singleton

```text
Double Checked Locking
```

---

# 14. Bill Pugh Singleton

Most recommended approach.

---

### Code

```java
public class DatabaseManager {

    private DatabaseManager() {
    }

    private static class Holder {

        private static final DatabaseManager INSTANCE =
                new DatabaseManager();
    }

    public static DatabaseManager getInstance() {
        return Holder.INSTANCE;
    }
}
```

---

### How It Works

When class loads:

```text
DatabaseManager Loaded
```

Holder NOT loaded.

---

First call:

```java
getInstance()
```

loads:

```text
Holder Class
```

then creates object.

---

### Advantages

- Lazy Loading
- Thread Safe
- No Synchronization Cost

---

### Recommended in Core Java

Yes.

---

# 15. Enum Singleton

Joshua Bloch's Recommended Singleton.

---

### Code

```java
public enum DatabaseManager {

    INSTANCE;

    public void connect() {
        System.out.println("Connected");
    }
}
```

Usage:

```java
DatabaseManager.INSTANCE.connect();
```

---

### Advantages

Automatically protects against:

- Reflection
- Serialization
- Cloning

---

### Most Secure Singleton

```text
Enum Singleton
```

---

### Drawback

Not suitable when lazy loading is needed.

---

# 16. Problems With Singleton

Singleton is not perfect.

---

## Reflection Attack

Reflection can access private constructor.

```java
Constructor<Singleton> c =
Singleton.class.getDeclaredConstructor();

c.setAccessible(true);
```

Can create new object.

---

## Serialization Attack

```java
ObjectOutputStream
ObjectInputStream
```

can create new object after deserialization.

---

## Cloning Attack

```java
clone()
```

creates another object.

---

# 17. How Enum Singleton Solves Everything

Enum internally prevents:

```text
Reflection
Serialization
Cloning
```

That's why many experts recommend it.

---

# 18. Real World Examples

## Database Connection Manager

```java
DatabaseManager.getInstance()
```

Only one manager.

---

## Logger

```java
Logger.getInstance()
```

One logging system.

---

## Cache Manager

```java
CacheManager.getInstance()
```

Shared cache.

---

## Configuration Manager

```java
ConfigManager.getInstance()
```

One configuration object.

---

# 19. Singleton in Spring Boot

Most Spring Beans are Singleton.

Example:

```java
@Service
public class EmployeeService {
}
```

Spring creates:

```text
Only One Object
```

for entire application.

---

### Verify

```java
@Autowired
EmployeeService service1;

@Autowired
EmployeeService service2;
```

Check:

```java
System.out.println(service1 == service2);
```

Output:

```text
true
```

---

# 20. Singleton vs Static Class

| Singleton | Static Class |
|------------|-------------|
| Object Exists | No Object |
| Supports Interfaces | No |
| Supports Inheritance | No |
| Lazy Loading Possible | No |
| Can Maintain State | Limited |

---

# 21. When To Use Singleton

Use when:

- Exactly one object required
- Shared resources
- Global configuration
- Logging
- Cache management

Examples:

```text
Logger
Cache
Database Manager
Configuration Manager
Thread Pool Manager
```

---

# 22. When NOT To Use Singleton

Avoid when:

- Multiple instances may be needed later
- Heavy unit testing required
- Object maintains user-specific state

Singleton often behaves like a global variable.

---

# 23. Interview Questions

### Q1. What is Singleton Pattern?

Singleton ensures only one instance of a class exists and provides a global access point.

---

### Q2. Why Constructor Is Private?

To prevent object creation using:

```java
new Singleton()
```

---

### Q3. Is Lazy Singleton Thread Safe?

No.

---

### Q4. Which Singleton Is Best?

For Core Java:

```text
Bill Pugh Singleton
```

For Maximum Safety:

```text
Enum Singleton
```

---

### Q5. Why Use volatile in Double Checked Locking?

To prevent instruction reordering and visibility issues.

---

### Q6. Which Pattern Does Spring Use For Beans?

```text
Singleton Scope
```

By default.

---

# Quick Revision

```text
Singleton Design Pattern
------------------------

Purpose:
Only one object in JVM.

Implementations:

1. Eager Initialization
   ✔ Thread Safe
   ✘ Memory Waste

2. Lazy Initialization
   ✔ Memory Efficient
   ✘ Not Thread Safe

3. Synchronized Method
   ✔ Thread Safe
   ✘ Slow

4. Single Check + Synchronized Block
   ✘ Wrong Approach

5. Double Checked Locking
   ✔ Thread Safe
   ✔ Fast

6. Bill Pugh Singleton
   ✔ Lazy
   ✔ Thread Safe
   ✔ Recommended

7. Enum Singleton
   ✔ Safest
   ✔ Reflection Safe
   ✔ Serialization Safe
   ✔ Clone Safe

Remember:

Eager  -> Create Early
Lazy   -> Create Later
Sync   -> Safe but Slow
DCL    -> Safe and Fast
Bill Pugh -> Best Java Implementation
Enum   -> Most Secure
```