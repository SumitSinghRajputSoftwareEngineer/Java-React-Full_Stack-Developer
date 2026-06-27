# Iterator Design Pattern

> **Goal:** Understand Iterator Design Pattern from scratch, why it exists, how it works internally, Java Iterator, ListIterator, Fail-Fast Iterators, Fail-Safe Iterators, Internal vs External Iterators, advantages, disadvantages, interview questions, and how it differs from Composite and Visitor.

---

# 1. What Problem Does Iterator Pattern Solve?

Suppose you have:

```java
ArrayList<String> names
```

and you want to traverse it.

---

Without Iterator:

```java
for(int i = 0; i < names.size(); i++) {

    System.out.println(names.get(i));
}
```

---

Works for:

```text
ArrayList
```

---

But what if collection changes to:

```text
LinkedList

HashSet

TreeSet

Custom Collection
```

---

Traversal logic changes.

---

Client becomes tightly coupled to collection structure.

---

Need a way to traverse collections without knowing:

```text
Internal Structure
```

---

This is exactly what Iterator Pattern solves.

---

# 2. Definition

> Iterator Pattern provides a way to access elements of a collection sequentially without exposing its underlying representation.

---

# 3. Simple Meaning

Instead of:

```text
Client
   ↓
Array Internals
```

Use:

```text
Client
   ↓
Iterator
   ↓
Collection
```

---

Client only knows:

```java
hasNext()

next()
```

---

Not:

```text
Array
Linked List
Tree
Hash Table
```

---

# 4. Real-Life Examples

---

## Example 1: TV Remote

You don't know how channels are stored.

---

You simply press:

```text
Next Channel
```

---

Remote acts like Iterator.

---

# Example 2: Music Playlist

You don't know:

```text
Array
Database
Queue
```

---

You simply:

```text
Next Song
```

---

Iterator behavior.

---

# Example 3: Book Pages

You don't care how pages are stored.

---

You just:

```text
Next Page
```

---

Iterator Pattern.

---

# 5. Core Idea

Separate:

```text
Storage Logic
```

from

```text
Traversal Logic
```

---

Collection stores data.

---

Iterator traverses data.

---

# 6. Components

---

## 1. Iterator

Traversal interface.

---

Example:

```java
Iterator<T>
```

---

## 2. Concrete Iterator

Actual traversal implementation.

---

Example:

```java
ArrayListIterator
```

---

## 3. Aggregate

Collection interface.

---

Example:

```java
List
```

---

## 4. Concrete Aggregate

Actual collection.

---

Example:

```java
ArrayList
```

---

# 7. UML Structure

```text
Client
   ↓
Iterator
   ↓
Collection
```

---

# 8. Iterator Interface

Most common methods:

```java
boolean hasNext();

T next();
```

---

Optional:

```java
remove();
```

---

# 9. Real-World Example

# Custom Book Collection

---

## Step 1: Iterator Interface

```java
public interface Iterator {

    boolean hasNext();

    String next();
}
```

---

# Step 2: Collection

```java
public class BookCollection {

    private String[] books;

    public BookCollection(
            String[] books) {

        this.books = books;
    }

    public Iterator iterator() {

        return new BookIterator(
                books);
    }
}
```

---

# Step 3: Concrete Iterator

```java
public class BookIterator
        implements Iterator {

    private String[] books;

    private int position;

    public BookIterator(
            String[] books) {

        this.books = books;
    }

    @Override
    public boolean hasNext() {

        return position <
                books.length;
    }

    @Override
    public String next() {

        return books[position++];
    }
}
```

---

# Step 4: Client

```java
BookCollection collection =
        new BookCollection(
                new String[]{
                        "Java",
                        "Spring",
                        "React"
                });

Iterator iterator =
        collection.iterator();

while(iterator.hasNext()) {

    System.out.println(
            iterator.next());
}
```

---

Output:

```text
Java

Spring

React
```

---

# 10. Internal Working

Collection:

```text
[Java, Spring, React]
```

---

Iterator maintains:

```java
position
```

---

Flow:

```text
position = 0

Java

position = 1

Spring

position = 2

React
```

---

Traversal state stored inside iterator.

---

# 11. Why Not Use For Loop?

For loop exposes:

```text
Collection Structure
```

---

Iterator hides:

```text
How Data Is Stored
```

---

Client code becomes independent.

---

# 12. Java Iterator

Most important real-world example.

---

Package:

```java
java.util.Iterator
```

---

Methods:

```java
hasNext()

next()

remove()
```

---

Example:

```java
List<String> names =
        List.of(
                "A",
                "B",
                "C");
```

---

```java
Iterator<String> iterator =
        names.iterator();

while(iterator.hasNext()) {

    System.out.println(
            iterator.next());
}
```

---

Classic Iterator Pattern.

---

# 13. ListIterator

Advanced Iterator.

---

Interface:

```java
ListIterator
```

---

Additional methods:

```java
hasPrevious()

previous()

add()

set()
```

---

Supports:

```text
Forward Traversal

Backward Traversal
```

---

Example:

```java
ListIterator<String> iterator =
        list.listIterator();
```

---

# 14. Internal Iterator vs External Iterator

Important interview topic.

---

## External Iterator

Client controls traversal.

---

Example:

```java
while(iterator.hasNext())
```

---

Client decides.

---

Java Iterator is:

```text
External Iterator
```

---

# 15. Internal Iterator

Collection controls traversal.

---

Example:

```java
list.forEach(
        System.out::println);
```

---

Traversal hidden.

---

Java Streams are closer to:

```text
Internal Iterator
```

---

# 16. Fail-Fast Iterator

Very important interview question.

---

Example:

```java
List<String> list =
        new ArrayList<>();
```

---

```java
Iterator<String> iterator =
        list.iterator();
```

---

While iterating:

```java
list.add("Java");
```

---

Throws:

```text
ConcurrentModificationException
```

---

This is:

```text
Fail-Fast Iterator
```

---

# Example

```java
List<String> list =
        new ArrayList<>();

list.add("A");

Iterator<String> iterator =
        list.iterator();

list.add("B");

iterator.next();
```

---

Output:

```text
ConcurrentModificationException
```

---

# 17. Why Fail-Fast?

Collections maintain:

```java
modCount
```

---

Iterator stores:

```java
expectedModCount
```

---

During iteration:

```java
modCount
!=
expectedModCount
```

---

Exception thrown.

---

# 18. Fail-Safe Iterator

Another interview favorite.

---

Works on:

```text
Copy Of Collection
```

---

Modification allowed.

---

Example:

```java
CopyOnWriteArrayList
```

---

```java
CopyOnWriteArrayList<String> list =
        new CopyOnWriteArrayList<>();
```

---

No exception.

---

# 19. Fail-Fast vs Fail-Safe

| Fail-Fast | Fail-Safe |
|------------|------------|
| Throws Exception | No Exception |
| Original Collection | Copy Collection |
| Faster | More Memory |
| ArrayList | CopyOnWriteArrayList |

---

Most important interview comparison.

---

# 20. Iterator Remove Method

Safe removal.

---

Wrong:

```java
list.remove(...)
```

during iteration.

---

Correct:

```java
iterator.remove();
```

---

Example:

```java
Iterator<String> iterator =
        list.iterator();

while(iterator.hasNext()) {

    String value =
            iterator.next();

    if(value.equals("A")) {

        iterator.remove();
    }
}
```

---

No exception.

---

# 21. Streams vs Iterator

Modern Java topic.

---

## Iterator

Pull Model.

---

```java
iterator.next();
```

---

Client requests data.

---

## Stream

Push Model.

---

```java
stream.forEach(...)
```

---

Framework handles traversal.

---

# 22. Java Examples

---

## ArrayList Iterator

```java
iterator()
```

---

## HashSet Iterator

```java
iterator()
```

---

## TreeSet Iterator

```java
iterator()
```

---

All use Iterator Pattern.

---

# 23. Spring Framework Examples

---

## BeanDefinition Iteration

Spring traverses bean collections.

---

## Handler Chains

Many Spring internals use iterators.

---

Concept appears frequently.

---

# 24. Advantages

---

## 1. Hides Internal Structure

Client independent.

---

## 2. Common Traversal API

Same code for:

```text
ArrayList

LinkedList

HashSet
```

---

## 3. Multiple Traversals Possible

Different iterators can coexist.

---

## 4. Single Responsibility Principle

Collection stores.

Iterator traverses.

---

# 25. Disadvantages

---

## 1. Additional Objects

Iterator object needed.

---

## 2. Slight Performance Overhead

Extra abstraction layer.

---

## 3. Concurrent Modification Issues

Fail-fast exceptions.

---

# 26. Iterator vs For Loop

---

## For Loop

Needs collection structure.

---

Example:

```java
list.get(i)
```

---

## Iterator

Works through interface.

---

Example:

```java
iterator.next()
```

---

More flexible.

---

# 27. Iterator vs Composite

---

## Composite

Represents tree structure.

---

Example:

```text
Folder
   ↓
Files
```

---

## Iterator

Traverses structure.

---

Example:

```text
Next Element
```

---

### Memory Trick

```text
Composite
      ↓
Store Tree

Iterator
      ↓
Traverse Tree
```

---

# 28. Iterator vs Visitor

---

## Iterator

Visits elements.

---

## Visitor

Adds new operations.

---

Different purposes.

---

### Memory Trick

```text
Iterator
      ↓
Traversal

Visitor
      ↓
Operation
```

---

# 29. When To Use Iterator

Use when:

- Collection traversal needed
- Internal structure hidden
- Common traversal API required

Examples:

```text
Lists
Sets
Trees
Graphs
Custom Collections
```

---

# 30. When NOT To Use

Avoid when:

- Collection extremely simple
- Direct indexing sufficient

---

# 31. Interview Questions

### Q1. What problem does Iterator solve?

Provides sequential access without exposing internal structure.

---

### Q2. What are the main methods?

```java
hasNext()

next()

remove()
```

---

### Q3. What is ListIterator?

Iterator supporting:

```text
Forward Traversal

Backward Traversal
```

---

### Q4. Difference Between Fail-Fast and Fail-Safe?

Fail-Fast:

```text
ConcurrentModificationException
```

Fail-Safe:

```text
No Exception
```

---

### Q5. Why does ConcurrentModificationException occur?

Because:

```java
modCount
!=
expectedModCount
```

---

### Q6. What is Internal Iterator?

```java
forEach()
```

style traversal.

---

### Q7. What is External Iterator?

```java
while(iterator.hasNext())
```

style traversal.

---

### Q8. Give Java examples.

```text
Iterator

ListIterator

ArrayList Iterator

HashSet Iterator
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
```

---

# Quick Revision

```text
Iterator Design Pattern
-----------------------

Purpose:
Traverse collection without exposing structure.

Components:

1. Iterator
2. Concrete Iterator
3. Aggregate
4. Concrete Aggregate

Important Methods:

hasNext()

next()

remove()

Types:

1. External Iterator
2. Internal Iterator

Important Concepts:

✔ Fail-Fast
✔ Fail-Safe
✔ ListIterator

Advantages:

✔ Hides Structure
✔ Common API
✔ Flexible Traversal

Disadvantages:

✘ Extra Objects
✘ Concurrent Modification Issues

Java Examples:

Iterator
ListIterator
ArrayList
HashSet

Remember:

Composite
      ↓
Store Tree

Iterator
      ↓
Traverse Tree
```