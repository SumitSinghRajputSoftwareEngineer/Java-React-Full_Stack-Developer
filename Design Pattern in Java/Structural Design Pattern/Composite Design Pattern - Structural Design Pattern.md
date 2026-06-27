stv# Composite Design Pattern

> **Goal:** Understand Composite Design Pattern from scratch, why it exists, Leaf vs Composite objects, Tree Structures, Recursive Operations, Transparent vs Safe Composite, Real-world examples, Java examples, Spring usage, advantages, disadvantages, and interview questions.

---

# 1. What Problem Does Composite Pattern Solve?

Suppose you are building a File Explorer.

You have:

```text
File
Folder
```

A folder can contain:

```text
Files
Folders
```

And those folders can contain:

```text
More Files
More Folders
```

---

Example:

```text
Documents
│
├── Resume.pdf
├── Notes.txt
│
└── Projects
      │
      ├── Java
      │    ├── Main.java
      │    └── App.java
      │
      └── React
            └── App.js
```

---

Now suppose user clicks:

```text
Calculate Size
```

Question:

Should we write separate logic for:

```text
File
Folder
```

?

---

Without Composite:

```java
if(object instanceof File)
{
    ...
}

if(object instanceof Folder)
{
    ...
}
```

Everywhere.

---

This becomes messy.

---

We need a way to treat:

```text
Single Object
```

and

```text
Group Of Objects
```

the same way.

---

This is exactly what Composite Pattern solves.

---

# 2. Definition

> Composite Pattern allows clients to treat individual objects and compositions of objects uniformly.

---

# 3. Simple Meaning

Suppose:

```text
File
```

is a single object.

---

And:

```text
Folder
```

is a collection of objects.

---

Composite Pattern says:

```text
Treat Both The Same
```

---

Example:

```java
component.showDetails();
```

works for:

```java
File
```

and

```java
Folder
```

---

# 4. Real-Life Example

## Company Organization

---

CEO has:

```text
Managers
```

Managers have:

```text
Employees
```

Employees may have:

```text
Interns
```

---

Structure:

```text
CEO
 │
 ├── Manager1
 │      ├── Employee1
 │      └── Employee2
 │
 └── Manager2
        └── Employee3
```

---

Question:

How to print organization structure?

---

Composite Pattern:

```text
Employee
```

becomes common component.

---

Both:

```text
Manager
Employee
```

implement same interface.

---

# 5. Tree Structure

Composite Pattern almost always forms a:

```text
Tree Structure
```

Example:

```text
Root
 │
 ├── Child
 │
 ├── Child
 │     │
 │     ├── Child
 │     └── Child
 │
 └── Child
```

---

Important Interview Point:

```text
Composite Pattern = Tree Structure
```

---

# 6. Core Components

Composite Pattern has three major components.

---

## 1. Component

Common interface.

Example:

```java
FileSystemComponent
```

---

## 2. Leaf

Individual object.

Example:

```java
File
```

---

## 3. Composite

Collection of objects.

Example:

```java
Folder
```

---

# 7. UML Structure

```text
              Component
                   ↑
        -----------------------
        |                     |
      File                 Folder
                               │
                      List<Component>
```

---

# 8. Real-World Example

# File System

---

## Step 1: Create Component

```java
public interface FileSystemComponent {

    void showDetails();
}
```

---

# Step 2: Create Leaf

## File

```java
public class File
        implements FileSystemComponent {

    private String name;

    public File(String name) {
        this.name = name;
    }

    @Override
    public void showDetails() {

        System.out.println(
                "File : " + name);
    }
}
```

---

# Step 3: Create Composite

## Folder

```java
import java.util.ArrayList;
import java.util.List;

public class Folder
        implements FileSystemComponent {

    private String name;

    private List<FileSystemComponent>
            components =
                new ArrayList<>();

    public Folder(String name) {
        this.name = name;
    }

    public void add(
            FileSystemComponent component) {

        components.add(component);
    }

    public void remove(
            FileSystemComponent component) {

        components.remove(component);
    }

    @Override
    public void showDetails() {

        System.out.println(
                "Folder : " + name);

        for(FileSystemComponent component
                : components) {

            component.showDetails();
        }
    }
}
```

---

# Step 4: Client Code

```java
File file1 =
        new File("Resume.pdf");

File file2 =
        new File("Notes.txt");

Folder javaFolder =
        new Folder("Java");

javaFolder.add(
        new File("Main.java"));

javaFolder.add(
        new File("App.java"));

Folder documents =
        new Folder("Documents");

documents.add(file1);
documents.add(file2);
documents.add(javaFolder);

documents.showDetails();
```

---

Output:

```text
Folder : Documents

File : Resume.pdf

File : Notes.txt

Folder : Java

File : Main.java

File : App.java
```

---

# 9. Internal Working

When:

```java
documents.showDetails();
```

executes.

---

Flow:

```text
Documents
    ↓
Resume.pdf

Notes.txt

Java Folder
    ↓
Main.java

App.java
```

---

Notice:

```java
showDetails()
```

called recursively.

---

This is the most important concept.

---

# 10. Recursive Nature

Composite Pattern usually uses:

```text
Recursion
```

---

Example:

```java
for(component : components)
{
    component.showDetails();
}
```

---

Because:

```text
Folder
can contain
Folder
```

---

Which again contains:

```text
Folder
```

---

Tree traversal happens recursively.

---

# 11. Leaf and Composite

Understanding this is important.

---

## Leaf

Cannot have children.

Example:

```text
File
```

---

Memory:

```text
File
```

No child nodes.

---

## Composite

Can have children.

Example:

```text
Folder
```

---

Memory:

```text
Folder
  ↓
Children
```

---

# 12. Transparent Composite

One implementation style.

---

Component contains:

```java
add()
remove()
```

methods.

---

Example:

```java
interface Component {

    void add(Component c);

    void remove(Component c);

    void display();
}
```

---

Problem:

Leaf must implement:

```java
add()
remove()
```

even though it doesn't need them.

---

Example:

```java
File.add()
```

doesn't make sense.

---

This is called:

```text
Transparent Composite
```

---

# 13. Safe Composite

Most preferred implementation.

---

Component contains only:

```java
display()
```

---

Composite contains:

```java
add()
remove()
```

---

Example:

```java
Component
    ↓
display()
```

---

```java
Folder
    ↓
add()
remove()
display()
```

---

Advantages:

```text
Cleaner Design
```

---

This is what most Java applications use.

---

# 14. Real-Life Example: Organization Hierarchy

---

## Component

```java
Employee
```

---

## Leaf

```java
Developer
Tester
```

---

## Composite

```java
Manager
Director
CEO
```

---

Tree:

```text
CEO
 │
 ├── Manager
 │      ├── Developer
 │      └── Tester
 │
 └── Manager
        └── Developer
```

---

# 15. Real-Life Example: Menu System

Restaurant menu.

---

```text
Food Menu
 │
 ├── Pizza
 ├── Burger
 │
 └── Drinks
      ├── Coke
      └── Pepsi
```

---

Both:

```text
Menu
Menu Item
```

can be treated uniformly.

---

# 16. Java Examples

---

## Swing Components

:contentReference[oaicite:0]{index=0}

Example:

```java
JPanel
```

contains:

```java
JButton
JTextField
JPanel
```

---

Composite structure.

---

## AWT Containers

```java
Container
```

contains:

```java
Components
```

---

# 17. Spring Framework Examples

Composite concepts appear in:

---

## Security Filter Chains

:contentReference[oaicite:1]{index=1}

```text
Filter Chain
```

contains:

```text
Multiple Filters
```

---

Hierarchy behaves like composite structure.

---

## Bean Definition Trees

Internally many hierarchical structures follow composite ideas.

---

# 18. Advantages

---

## 1. Uniform Treatment

Client treats:

```text
File
Folder
```

same way.

---

## 2. Simplifies Client Code

No need for:

```java
instanceof
```

checks everywhere.

---

## 3. Easy Tree Structures

Perfect for:

```text
Hierarchies
```

---

## 4. Open/Closed Principle

Add new leaf types easily.

---

## 5. Recursive Operations Become Easy

One method handles entire tree.

---

# 19. Disadvantages

---

## 1. Difficult To Restrict Components

Sometimes:

```text
Certain children
```

should not be allowed.

---

## 2. Can Become Too Generic

All components forced into common interface.

---

## 3. Recursion Complexity

Very deep trees may become harder to debug.

---

# 20. Composite vs Decorator

Most confusing comparison.

---

## Composite

Represents:

```text
Part-Whole Hierarchy
```

---

Example:

```text
Folder
    ↓
Files
```

---

## Decorator

Represents:

```text
Additional Behavior
```

---

Example:

```text
Coffee
   ↓
Milk
   ↓
Sugar
```

---

### Memory Trick

```text
Composite
     ↓
Tree Structure

Decorator
     ↓
Behavior Layer
```

---

# 21. Composite vs Bridge

---

## Composite

Focus:

```text
Tree Structure
```

---

## Bridge

Focus:

```text
Class Explosion
```

---

Example:

```text
Folder → Files
```

Composite.

---

Example:

```text
Remote → TV
```

Bridge.

---

# 22. Composite vs Flyweight

---

## Composite

Organizes objects.

---

## Flyweight

Shares objects.

---

Different goals.

---

# 23. When To Use Composite Pattern

Use when:

- Tree structures exist
- Hierarchies exist
- Recursive processing needed
- Client should treat groups and individuals uniformly

Examples:

```text
File System
Organization Hierarchy
Menus
UI Components
XML/HTML DOM Trees
```

---

# 24. When NOT To Use

Avoid when:

- No hierarchy exists
- No parent-child relationship exists
- Simple objects only

---

# 25. Interview Questions

### Q1. What problem does Composite Pattern solve?

Treats:

```text
Single Objects
and
Groups Of Objects
```

uniformly.

---

### Q2. What are the participants?

```text
Component
Leaf
Composite
```

---

### Q3. Which data structure is commonly used?

```text
Tree Structure
```

---

### Q4. What is a Leaf?

Object with no children.

Example:

```text
File
```

---

### Q5. What is a Composite?

Object that can contain children.

Example:

```text
Folder
```

---

### Q6. Why is recursion common?

Because:

```text
Composite
contains
Composite
```

forming a tree.

---

### Q7. Difference Between Transparent and Safe Composite?

Transparent:

```text
add()
remove()
inside Component
```

Safe:

```text
add()
remove()
inside Composite only
```

---

### Q8. Give real-world examples.

```text
File System
Organization Chart
Menus
Swing Components
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
and Implementation

Composite
    ↓
Tree Structure
Treat Part and Whole
the Same
```

---

# Quick Revision

```text
Composite Design Pattern
------------------------

Purpose:
Treat individual objects and groups uniformly.

Structure:

Component
   ↑
Leaf

Component
   ↑
Composite
   ↓
Children

Key Concepts:

✔ Tree Structure
✔ Recursion
✔ Parent-Child Relationship
✔ Uniform Processing

Types:

1. Transparent Composite
2. Safe Composite

Advantages:

✔ Uniform Handling
✔ Easy Hierarchy Management
✔ Recursive Processing

Disadvantages:

✘ More Generic Design
✘ Recursive Complexity

Examples:

File System
Organization Structure
Menu System
Swing Components

Remember:

Composite
      ↓
Tree Structure

Leaf
      ↓
No Children

Composite
      ↓
Has Children
```s