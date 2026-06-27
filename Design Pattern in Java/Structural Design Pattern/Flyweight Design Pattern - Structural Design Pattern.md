# Flyweight Design Pattern

> **Goal:** Understand Flyweight Design Pattern from scratch, why it exists, intrinsic vs extrinsic state, immutable shared objects, caching, object pools, Java examples, Spring usage, advantages, disadvantages, interview questions, and how it differs from Singleton, Prototype, and Composite.

---

# 1. What Problem Does Flyweight Pattern Solve?

Suppose you are building a game.

The game contains:

```text
1,000,000 Trees
```

---

Each tree has:

```text
Tree Type
Color
Texture
Height
Position X
Position Y
```

---

Without Flyweight:

```java
new Tree(...)
```

created:

```text
1,000,000 times
```

---

Memory Usage:

```text
Huge
```

---

Most data is actually identical:

```text
Tree Type = Mango
Color = Green
Texture = Same
```

---

Only positions differ.

---

Creating:

```text
1 Million Full Objects
```

wastes memory.

---

Need a better solution.

---

# 2. Definition

> Flyweight Pattern reduces memory usage by sharing common object state among multiple objects.

---

# 3. Simple Meaning

Instead of:

```text
Create
1,000,000 Objects
```

---

Create:

```text
One Shared Tree Object
```

---

Store changing values separately.

---

Memory becomes:

```text
Very Small
```

---

# 4. Real-Life Example

## Characters in MS Word

Suppose document contains:

```text
AAAAAAAAAAAAAAAAAAAAA
```

100,000 times.

---

Would Word create:

```text
100,000 A Objects ?
```

No.

---

It stores:

```text
Character A
```

once.

---

Position information differs.

---

This is Flyweight Pattern.

---

# 5. Core Idea

Split object state into:

```text
1. Intrinsic State
2. Extrinsic State
```

---

This is the MOST IMPORTANT concept.

---

# 6. Intrinsic State

Shared state.

---

Never changes.

---

Stored inside Flyweight object.

---

Example:

```text
Tree Type
Color
Texture
```

---

Shared by all trees.

---

# 7. Extrinsic State

Non-shared state.

---

Changes per object.

---

Stored outside Flyweight.

---

Example:

```text
Position X
Position Y
```

---

Different for every tree.

---

# 8. Memory Visualization

---

## Without Flyweight

```text
Tree1
Tree2
Tree3
Tree4
Tree5
```

Each contains:

```text
Color
Texture
Type
Position
```

Repeated.

---

Memory:

```text
Huge
```

---

## With Flyweight

```text
One Shared TreeType
```

---

Each tree stores only:

```text
X
Y
```

---

Memory:

```text
Very Small
```

---

# 9. Components of Flyweight

---

## 1. Flyweight

Shared object.

---

Example:

```java
TreeType
```

---

## 2. Concrete Flyweight

Actual implementation.

---

Example:

```java
MangoTree
```

---

## 3. Flyweight Factory

Creates and caches Flyweights.

---

Most important class.

---

## 4. Client

Uses Flyweights.

---

# 10. UML Structure

```text
Client
   ↓
FlyweightFactory
   ↓
Flyweight
```

---

# 11. Real-World Example

# Forest Game

---

## Step 1: Flyweight

```java
public class TreeType {

    private String name;

    private String color;

    public TreeType(
            String name,
            String color) {

        this.name = name;
        this.color = color;
    }

    public void draw(
            int x,
            int y) {

        System.out.println(
                name +
                " Tree at "
                + x +
                "," +
                y);
    }
}
```

---

Notice:

```java
name
color
```

are shared.

---

# Step 2: Flyweight Factory

```java
import java.util.HashMap;
import java.util.Map;

public class TreeFactory {

    private static Map<String, TreeType>
            cache =
            new HashMap<>();

    public static TreeType getTreeType(
            String name,
            String color) {

        String key =
                name + color;

        if(!cache.containsKey(key)) {

            cache.put(
                    key,
                    new TreeType(
                            name,
                            color));
        }

        return cache.get(key);
    }
}
```

---

This is the heart of Flyweight Pattern.

---

Objects are cached.

---

# Step 3: Context Object

```java
public class Tree {

    private int x;

    private int y;

    private TreeType treeType;

    public Tree(
            int x,
            int y,
            TreeType treeType) {

        this.x = x;
        this.y = y;
        this.treeType = treeType;
    }

    public void draw() {

        treeType.draw(x, y);
    }
}
```

---

Notice:

```java
x
y
```

stored separately.

---

These are:

```text
Extrinsic State
```

---

# Step 4: Client

```java
TreeType mangoTree =
        TreeFactory.getTreeType(
                "Mango",
                "Green");

Tree tree1 =
        new Tree(
                10,
                20,
                mangoTree);

Tree tree2 =
        new Tree(
                30,
                50,
                mangoTree);
```

---

Memory:

```text
tree1
     ↓

tree2
     ↓

Same TreeType Object
```

---

# 12. Internal Working

---

Without Flyweight:

```text
TreeType1
TreeType2
TreeType3
TreeType4
```

---

With Flyweight:

```text
Tree1
  ↓

Tree2
  ↓

Tree3
  ↓

Same TreeType
```

---

Huge memory savings.

---

# 13. Intrinsic vs Extrinsic State

This is the most asked interview question.

---

## Intrinsic State

Shared.

Stored inside Flyweight.

---

Example:

```text
Color
Texture
Tree Type
```

---

## Extrinsic State

Not shared.

Passed by client.

---

Example:

```text
Position
Coordinates
```

---

### Memory Trick

```text
Intrinsic
     ↓
Inside Flyweight

Extrinsic
     ↓
External
```

---

# 14. Why Is Factory Necessary?

Without Factory:

```java
new TreeType(...)
```

every time.

---

No sharing.

---

Factory ensures:

```text
Reuse Existing Object
```

---

This caching mechanism makes Flyweight possible.

---

# 15. Immutable Flyweights

Important best practice.

---

Flyweights should usually be:

```text
Immutable
```

---

Example:

```java
final String color;
```

---

Why?

Because:

```text
Shared Object
```

should not change.

---

Otherwise:

```java
tree1.color = "Red";
```

affects:

```java
tree2
```

too.

---

Dangerous.

---

# 16. Flyweight Cache

Most Flyweight implementations use:

```java
Map<Key, Flyweight>
```

---

Example:

```java
Map<String, TreeType>
```

---

This cache stores:

```text
Already Created Objects
```

---

Very important interview point.

---

# 17. Real-Life Example

# Text Editor

Suppose document contains:

```text
100,000 letters
```

---

Store:

```text
Font
Size
Style
```

once.

---

Store:

```text
Position
```

separately.

---

Huge memory optimization.

---

# 18. Real-Life Example

# Chess Game

Pieces:

```text
White Pawn
Black Pawn
White Rook
Black Rook
```

---

Can be shared.

---

Position differs.

---

Perfect Flyweight use case.

---

# 19. Java Examples

Several Java classes use Flyweight concepts.

---

## Integer Cache

Most famous example.

---

Example:

```java
Integer a = 100;

Integer b = 100;
```

---

Check:

```java
System.out.println(a == b);
```

Output:

```text
true
```

---

Why?

Because JVM caches:

```text
-128 to 127
```

Integer objects.

---

This is Flyweight.

---

## String Pool

Another classic example.

---

Example:

```java
String s1 = "Hello";

String s2 = "Hello";
```

---

Memory:

```text
One String Object
```

shared.

---

Flyweight pattern.

---

# 20. Spring Framework Examples

---

## Bean Definitions

Spring stores metadata once.

---

## Singleton Beans

Not Flyweight directly.

But shared-object idea is similar.

---

Important Interview Trap:

```text
Singleton ≠ Flyweight
```

---

# 21. Flyweight vs Singleton

Very common interview question.

---

## Singleton

Only:

```text
One Object
```

exists.

---

Example:

```java
DatabaseConnection
```

---

## Flyweight

Many logical objects.

Shared state.

---

Example:

```text
1 Million Trees
```

using:

```text
10 Shared Tree Types
```

---

### Memory Trick

```text
Singleton
      ↓
One Object

Flyweight
      ↓
Many Objects
Share State
```

---

# 22. Flyweight vs Prototype

---

## Prototype

Creates copies.

---

Example:

```java
prototype.clone()
```

---

## Flyweight

Shares objects.

---

Example:

```java
factory.getTreeType()
```

returns same object.

---

### Memory Trick

```text
Prototype
     ↓
Copy

Flyweight
     ↓
Share
```

---

# 23. Flyweight vs Composite

---

## Composite

Organizes objects into trees.

---

Example:

```text
Folder → Files
```

---

## Flyweight

Optimizes memory.

---

Example:

```text
Trees Sharing TreeType
```

---

Different purposes.

---

# 24. Object Pool vs Flyweight

Advanced Interview Question.

---

## Flyweight

Shares immutable state.

---

Example:

```java
String Pool
```

---

## Object Pool

Reuses expensive objects.

---

Example:

```java
Database Connections
```

---

Object returned to pool.

---

Different concepts.

---

# 25. Advantages

---

## 1. Huge Memory Savings

Most important benefit.

---

## 2. Better Performance

Less object creation.

---

## 3. Reduced Garbage Collection

Fewer objects.

---

## 4. Scales Well

Millions of logical objects possible.

---

# 26. Disadvantages

---

## 1. More Complex Design

Need:

```text
Factory
Cache
State Separation
```

---

## 2. Hard To Maintain

Intrinsic/Extrinsic separation required.

---

## 3. Shared Object Risks

Mutable flyweights can cause bugs.

---

# 27. When To Use Flyweight

Use when:

- Huge number of objects
- Most state is identical
- Memory usage high
- Shared immutable state possible

Examples:

```text
Trees
Characters
Icons
Fonts
Chess Pieces
Game Objects
```

---

# 28. When NOT To Use

Avoid when:

- Few objects exist
- State differs significantly
- Sharing not possible

---

# 29. Interview Questions

### Q1. What problem does Flyweight solve?

Reduces memory usage by sharing common state.

---

### Q2. What are Intrinsic and Extrinsic State?

Intrinsic:

```text
Shared State
```

Extrinsic:

```text
External State
```

---

### Q3. Why is Factory important?

Factory caches and reuses Flyweights.

---

### Q4. Should Flyweights be immutable?

```text
YES
```

to avoid shared-state bugs.

---

### Q5. Give Java examples.

```text
Integer Cache
String Pool
```

---

### Q6. Difference Between Flyweight and Singleton?

Singleton:

```text
One Object
```

Flyweight:

```text
Many Objects
Share Common State
```

---

### Q7. Difference Between Flyweight and Prototype?

Prototype:

```text
Clone
```

Flyweight:

```text
Share
```

---

### Q8. What is the biggest benefit?

```text
Memory Optimization
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
Share Objects
Save Memory
```

---

# Quick Revision

```text
Flyweight Design Pattern
------------------------

Purpose:
Reduce memory usage by sharing objects.

Key Concepts:

1. Intrinsic State
   ✔ Shared
   ✔ Immutable

2. Extrinsic State
   ✔ External
   ✔ Changes Per Object

Structure:

Client
   ↓
FlyweightFactory
   ↓
Flyweight

Advantages:

✔ Huge Memory Savings
✔ Better Performance
✔ Fewer Objects

Disadvantages:

✘ More Complexity
✘ Shared State Risks

Java Examples:

✔ Integer Cache
✔ String Pool

Remember:

Prototype
      ↓
Copy Objects

Flyweight
      ↓
Share Objects

Singleton
      ↓
One Object

Flyweight
      ↓
Many Objects
Shared State
```