# Interpreter Design Pattern

> **Goal:** Understand Interpreter Design Pattern from scratch, why it exists, how it works internally, Grammar Rules, Abstract Syntax Tree (AST), Terminal Expressions, Non-Terminal Expressions, Parsing, Java examples, SQL-like examples, advantages, disadvantages, interview questions, and how it differs from Strategy, Command, and Visitor.

---

# 1. What Problem Does Interpreter Pattern Solve?

Suppose we want to evaluate:

```text
2 + 3
```

or

```text
10 - 5
```

or

```text
A AND B
```

or

```text
A OR B
```

---

Without Interpreter Pattern:

We may write:

```java
if(expression.equals("+"))
{
    ...
}
else if(expression.equals("-"))
{
    ...
}
else if(expression.equals("*"))
{
    ...
}
```

---

As grammar grows:

```text
+
-
*
/
AND
OR
NOT
```

---

Code becomes:

```text
Huge
Difficult To Extend
```

---

Need a way to represent:

```text
Grammar Rules
```

as objects.

---

This is where Interpreter Pattern comes in.

---

# 2. Definition

> Interpreter Pattern defines a grammar for a language and provides an interpreter to evaluate sentences of that language.

---

# 3. Simple Meaning

Suppose we create a mini language:

```text
5 + 3
```

---

Interpreter understands:

```text
Number
Plus
Minus
```

---

Every grammar rule becomes:

```text
A Class
```

---

Expression is represented as:

```text
Objects
```

instead of strings.

---

# 4. Real-Life Examples

---

## Example 1: Calculator

Input:

```text
5 + 10
```

---

Interpreter evaluates:

```text
15
```

---

# Example 2: SQL Query Engine

Input:

```sql
SELECT * FROM USERS
WHERE AGE > 18
```

---

Database interprets grammar.

---

# Example 3: Search Filters

Input:

```text
Age > 18 AND City = Bangalore
```

---

Interpreter evaluates expression.

---

# Example 4: Rule Engine

Input:

```text
Salary > 50000 AND Experience > 5
```

---

Decision engine interprets rule.

---

# 5. Core Idea

Represent language grammar as:

```text
Object Tree
```

---

Each node knows how to:

```text
Interpret Itself
```

---

# 6. Important Terminologies

Most important interview section.

---

# Grammar

Rules of language.

---

Example:

```text
Expression
=
Number + Number
```

---

# Sentence

Actual input.

---

Example:

```text
5 + 10
```

---

# Context

Stores information required during interpretation.

---

Example:

```java
A = true
B = false
```

---

# Abstract Syntax Tree (AST)

Tree representation of expression.

---

Example:

```text
      +
     / \
    5   3
```

---

Very important concept.

---

# 7. Components

---

## 1. Abstract Expression

Common interface.

---

Example:

```java
Expression
```

---

## 2. Terminal Expression

Leaf node.

---

Example:

```text
Number
Boolean
Variable
```

---

## 3. Non-Terminal Expression

Contains other expressions.

---

Example:

```text
Addition
Subtraction
AND
OR
```

---

## 4. Context

Stores external data.

---

## 5. Client

Builds expression tree.

---

# 8. UML Structure

```text
Expression
     ↑

 -----------------
 ↑               ↑

Number      AddExpression
                 ↑
             Expression
```

---

# 9. Real-World Example

# Arithmetic Interpreter

Evaluate:

```text
10 + 20
```

---

# Step 1: Expression Interface

```java
public interface Expression {

    int interpret();
}
```

---

# Step 2: Terminal Expression

```java
public class Number
        implements Expression {

    private int value;

    public Number(int value) {

        this.value = value;
    }

    @Override
    public int interpret() {

        return value;
    }
}
```

---

Leaf node.

---

# Step 3: Non-Terminal Expression

```java
public class AddExpression
        implements Expression {

    private Expression left;

    private Expression right;

    public AddExpression(
            Expression left,
            Expression right) {

        this.left = left;
        this.right = right;
    }

    @Override
    public int interpret() {

        return left.interpret()
                +
                right.interpret();
    }
}
```

---

Notice:

```java
private Expression left;
private Expression right;
```

---

Expression tree created.

---

# Step 4: Client

```java
Expression expression =
        new AddExpression(
                new Number(10),
                new Number(20));

System.out.println(
        expression.interpret());
```

---

Output:

```text
30
```

---

# 10. Internal Working

Tree:

```text
        +
       / \
      10 20
```

---

Execution:

```text
AddExpression
      ↓
left.interpret()
      ↓
10

right.interpret()
      ↓
20

10 + 20
```

---

Result:

```text
30
```

---

# 11. More Complex Example

Expression:

```text
(10 + 20) - 5
```

---

AST:

```text
         -
       /   \
      +     5
    /   \
  10    20
```

---

Code:

```java
Expression expression =
        new SubtractExpression(
                new AddExpression(
                        new Number(10),
                        new Number(20)),
                new Number(5));
```

---

Result:

```text
25
```

---

# 12. Boolean Interpreter Example

Expression:

```text
TRUE AND FALSE
```

---

Terminal:

```java
BooleanExpression
```

---

Non-Terminal:

```java
AndExpression
```

---

Result:

```text
false
```

---

Very common interview example.

---

# 13. Context-Based Example

Expression:

```text
A AND B
```

---

Variables:

```text
A = true
B = false
```

---

Need Context.

---

```java
Map<String, Boolean>
```

---

Context stores values.

---

Interpreter evaluates.

---

# 14. Abstract Syntax Tree (AST)

Most important advanced concept.

---

Expression:

```text
A AND (B OR C)
```

---

AST:

```text
        AND
       /   \
      A     OR
           /  \
          B    C
```

---

Every node:

```java
interpret()
```

---

recursively.

---

# 15. Recursive Nature

Interpreter heavily uses:

```text
Recursion
```

---

Example:

```java
left.interpret()

right.interpret()
```

---

Every node interprets child nodes.

---

# 16. SQL Parser Example

Query:

```sql
AGE > 18 AND CITY='BLR'
```

---

AST:

```text
          AND
         /   \
 AGE>18       CITY='BLR'
```

---

Each condition:

```text
Expression
```

---

Common use case.

---

# 17. Expression Tree Example

Expression:

```text
2 + 3 * 5
```

---

AST:

```text
       +
      / \
     2   *
        / \
       3   5
```

---

Result:

```text
17
```

---

This is how calculators work internally.

---

# 18. Java Examples

Interpreter Pattern itself is not common in JDK APIs.

---

But concepts appear in:

```text
Regular Expressions

Expression Language

SQL Parsers

XPath

SpEL
```

---

# 19. Spring Framework Example

## Spring Expression Language (SpEL)

:contentReference[oaicite:0]{index=0}

Example:

```java
#{2 + 3}
```

---

Spring interprets expression.

---

Another example:

```java
#{employee.salary > 50000}
```

---

SpEL uses Interpreter concepts.

---

# 20. Hibernate Example

Query:

```java
criteria.where(...)
```

---

Internally converted to expression tree.

---

Interpreter-like processing.

---

# 21. Advantages

---

## 1. Easy To Extend Grammar

Add:

```text
Multiply
Divide
Modulo
```

---

Create new classes.

---

## 2. Grammar Rules Encapsulated

Each rule has separate class.

---

## 3. Follows Open/Closed Principle

New expressions without modifying old ones.

---

## 4. Easy To Build DSLs

DSL:

```text
Domain Specific Language
```

---

Examples:

```text
SQL
Regex
Rule Engines
```

---

# 22. Disadvantages

---

## 1. Many Classes

Large grammar creates many classes.

---

## 2. Complex For Large Languages

Compilers use more advanced techniques.

---

## 3. Performance Issues

Deep recursion possible.

---

# 23. Interpreter vs Strategy

---

## Strategy

Chooses algorithm.

---

Example:

```text
UPI
Card
PayPal
```

---

## Interpreter

Evaluates grammar.

---

Example:

```text
A AND B
```

---

### Memory Trick

```text
Strategy
      ↓
Choose Algorithm

Interpreter
      ↓
Interpret Language
```

---

# 24. Interpreter vs Command

---

## Command

Represents action.

---

Example:

```text
Delete File
Send Email
```

---

## Interpreter

Represents grammar rule.

---

Example:

```text
AND
OR
PLUS
MINUS
```

---

### Memory Trick

```text
Command
      ↓
Action

Interpreter
      ↓
Language
```

---

# 25. Interpreter vs Visitor

---

## Interpreter

Adds grammar.

---

Example:

```text
AND
OR
NOT
```

---

## Visitor

Adds operations.

---

Example:

```text
Print
Validate
Export
```

---

Different purposes.

---

# 26. When To Use Interpreter

Use when:

- Grammar is simple
- Language rules are fixed
- Expression trees needed
- Rule engines required

Examples:

```text
SQL Filters
Rule Engines
Calculators
Regex
Expression Languages
```

---

# 27. When NOT To Use

Avoid when:

- Grammar very large
- Compiler-level complexity
- Performance critical systems

---

# 28. Interview Questions

### Q1. What problem does Interpreter solve?

Defines grammar and interprets language sentences.

---

### Q2. What are Terminal Expressions?

Leaf nodes.

Examples:

```text
Number
Variable
Boolean
```

---

### Q3. What are Non-Terminal Expressions?

Contain other expressions.

Examples:

```text
AND
OR
PLUS
MINUS
```

---

### Q4. What is AST?

```text
Abstract Syntax Tree
```

representation of expression.

---

### Q5. What role does Context play?

Stores external values used during interpretation.

---

### Q6. Give Spring example.

```text
SpEL
(Spring Expression Language)
```

---

### Q7. Why is recursion important?

Expression tree is evaluated recursively.

---

### Q8. What is the biggest disadvantage?

Large grammars create many classes.

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
```

---

# Quick Revision

```text
Interpreter Design Pattern
--------------------------

Purpose:
Interpret a language grammar.

Components:

1. Abstract Expression
2. Terminal Expression
3. Non-Terminal Expression
4. Context
5. Client

Key Concepts:

✔ Grammar
✔ AST
✔ Recursion
✔ Expression Tree

Examples:

Calculator
SQL Parser
Rule Engine
Regex
SpEL

Advantages:

✔ Easy Grammar Extension
✔ Encapsulation
✔ Open/Closed Principle

Disadvantages:

✘ Many Classes
✘ Complex For Large Languages

Remember:

Strategy
      ↓
Choose Algorithm

Command
      ↓
Execute Request

Interpreter
      ↓
Interpret Language
```