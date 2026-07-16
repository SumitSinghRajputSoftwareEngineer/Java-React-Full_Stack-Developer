# @EntityGraph

## Definition

`@EntityGraph` is a Spring Data JPA annotation used to control **which related entities should be fetched eagerly in a specific query**, without changing the default fetch type of the entity.

It tells Hibernate/JPA:

> Fetch these associations in the same query to avoid N+1 query problems.

Package:

```java
import org.springframework.data.jpa.repository.EntityGraph;
```

---

# Why Do We Need @EntityGraph?

Consider:

```java
Order
    ↓
Customer

Order
    ↓
Items
```

---

Entity:

```java
@Entity
public class Order {

    @Id
    private Long id;

    @ManyToOne(
        fetch = FetchType.LAZY
    )
    private Customer customer;

    @OneToMany(
        mappedBy = "order",
        fetch = FetchType.LAZY
    )
    private List<OrderItem> items;
}
```

---

Everything is:

```java
LAZY
```

---

Query:

```java
List<Order> orders =
    orderRepository.findAll();
```

---

Hibernate SQL:

```sql
SELECT * FROM orders;
```

---

Later:

```java
order.getCustomer();
```

---

Hibernate:

```sql
SELECT * FROM customer
WHERE id = ?
```

---

For 100 orders:

```text
1 query for orders
+
100 queries for customers
```

---

Problem:

```text
N+1 Query Problem
```

---

Solution:

```java
@EntityGraph
```

---

# What Is N+1 Problem?

Suppose:

```java
List<Order> orders =
    orderRepository.findAll();
```

---

Generated:

```sql
SELECT * FROM orders;
```

---

Loop:

```java
for(Order order : orders) {
    order.getCustomer().getName();
}
```

---

Generated:

```sql
SELECT * FROM customer WHERE id=1;
SELECT * FROM customer WHERE id=2;
SELECT * FROM customer WHERE id=3;
...
```

---

Total:

```text
1 + N Queries
```

---

Bad performance.

---

# Basic Syntax

```java
@EntityGraph(
    attributePaths = {
        "customer"
    }
)
```

---

Repository:

```java
@EntityGraph(
    attributePaths = {
        "customer"
    }
)
List<Order> findAll();
```

---

Meaning:

```text
Fetch Customer Along With Order
```

---

# Example

## Order Entity

```java
@Entity
public class Order {

    @Id
    private Long id;

    @ManyToOne(
        fetch = FetchType.LAZY
    )
    private Customer customer;
}
```

---

## Repository

```java
@Repository
public interface OrderRepository
extends JpaRepository<Order, Long> {

    @EntityGraph(
        attributePaths = {
            "customer"
        }
    )
    List<Order> findAll();
}
```

---

# Generated SQL

Hibernate creates:

```sql
SELECT
    o.*,
    c.*
FROM orders o

LEFT JOIN customer c
ON o.customer_id = c.id
```

---

Single Query.

---

No N+1 issue.

---

# Fetching Multiple Associations

Example:

```java
@EntityGraph(
    attributePaths = {
        "customer",
        "items"
    }
)
```

---

Repository:

```java
@EntityGraph(
    attributePaths = {
        "customer",
        "items"
    }
)
List<Order> findAll();
```

---

Fetches:

```text
Order
Customer
Items
```

in one query.

---

# Nested Association Fetching

Advanced Feature.

---

Example:

```java
Order
   ↓
Customer
   ↓
Address
```

---

Repository:

```java
@EntityGraph(
    attributePaths = {
        "customer",
        "customer.address"
    }
)
```

---

Fetches:

```text
Order
Customer
Address
```

together.

---

# Real Production Example

## User

```java
@Entity
public class User {

    @Id
    private Long id;

    @OneToMany(
        mappedBy = "user",
        fetch = FetchType.LAZY
    )
    private List<Order> orders;
}
```

---

Repository:

```java
@EntityGraph(
    attributePaths = {
        "orders"
    }
)
Optional<User>
findById(Long id);
```

---

Generated:

```sql
SELECT
 u.*,
 o.*
FROM user u

LEFT JOIN orders o
ON u.id=o.user_id
```

---

Orders loaded immediately.

---

# EntityGraph Types

Important Interview Topic.

---

Two types:

```java
FETCH
LOAD
```

---

# FETCH Graph

Default.

---

```java
@EntityGraph(
    type =
      EntityGraphType.FETCH
)
```

---

Meaning:

```text
Only Specified Attributes
Become Eager
```

---

Everything else:

```text
Lazy
```

---

# LOAD Graph

```java
@EntityGraph(
    type =
      EntityGraphType.LOAD
)
```

---

Meaning:

```text
Specified Attributes
+
Existing Eager Attributes
```

remain loaded.

---

Comparison:

| Type | Behavior |
|--------|----------|
| FETCH | Only graph attributes loaded eagerly |
| LOAD | Graph attributes + existing eager attributes |

---

# Named Entity Graph

JPA Feature.

---

Instead of writing graph repeatedly.

---

Entity:

```java
@Entity

@NamedEntityGraph(
    name = "order.customer",

    attributeNodes = {
        @NamedAttributeNode(
            "customer"
        )
    }
)
public class Order {
}
```

---

Repository:

```java
@EntityGraph(
    value =
       "order.customer"
)
List<Order> findAll();
```

---

Reusable.

---

# @EntityGraph vs FetchType.EAGER

Interview Favorite.

---

## EAGER

```java
@ManyToOne(
    fetch =
       FetchType.EAGER
)
```

---

Always loads.

---

Every query.

---

Even when not needed.

---

Can hurt performance.

---

## EntityGraph

```java
@EntityGraph(...)
```

---

Loads association only for a specific query.

---

Much more flexible.

---

Comparison:

| Feature | EAGER | EntityGraph |
|----------|--------|------------|
| Always Loaded | ✅ | ❌ |
| Query Specific | ❌ | ✅ |
| Performance Control | Poor | Excellent |
| Recommended | ❌ | ✅ |

---

# @EntityGraph vs JOIN FETCH

Another Interview Favorite.

---

## JOIN FETCH

```java
@Query("""
SELECT o
FROM Order o

JOIN FETCH
o.customer
""")
```

---

Explicit JPQL.

---

## EntityGraph

```java
@EntityGraph(
    attributePaths={
       "customer"
    }
)
```

---

Cleaner.

---

No custom query required.

---

Comparison:

| Feature | JOIN FETCH | EntityGraph |
|----------|------------|------------|
| JPQL Needed | ✅ | ❌ |
| Reusable | Moderate | High |
| Cleaner | ❌ | ✅ |
| Dynamic | ❌ | ✅ |

---

# Internal Hibernate Processing

Spring Data detects:

```java
@EntityGraph
```

---

Creates JPA Fetch Graph.

---

Hibernate modifies SQL:

```sql
JOIN
```

---

Associations loaded immediately.

---

No additional lazy queries.

---

# Common Mistakes

## Mistake 1

Using EAGER Everywhere

Wrong:

```java
@ManyToOne(
 fetch = EAGER
)
```

---

Leads to:

```text
Huge Queries
Memory Issues
```

---

Use:

```java
LAZY
+
@EntityGraph
```

---

# Mistake 2

Loading Large Collections

Example:

```java
orders
payments
shipments
products
reviews
```

all together.

---

Massive joins.

---

Performance degradation.

---

# Mistake 3

Wrong Attribute Name

Wrong:

```java
@EntityGraph(
 attributePaths={
   "custmer"
 }
)
```

---

Runtime exception.

---

Must match field names exactly.

---

# Common Interview Questions

## What is @EntityGraph?

Controls eager fetching for a specific query.

---

## Why use it?

Avoid N+1 queries.

---

## Is it better than EAGER?

Yes.

More flexible.

---

## Difference Between JOIN FETCH and EntityGraph?

JPQL-based vs metadata-based fetching.

---

## Can it fetch nested associations?

Yes.

Example:

```java
customer.address
```

---

## Default type?

```java
EntityGraphType.FETCH
```

---

# Production Best Practice

Use:

```java
FetchType.LAZY
```

everywhere.

---

Then selectively fetch:

```java
@EntityGraph
```

where required.

---

Recommended pattern:

```java
@ManyToOne(
 fetch = LAZY
)
```

---

Repository:

```java
@EntityGraph(
 attributePaths={
   "customer"
 }
)
```

---

Provides:

```text
Better Performance
Less Memory Usage
No N+1 Queries
```

---

# Key Points To Remember

- `@EntityGraph` controls fetch behavior at query level.
- Helps solve N+1 query problems.
- Works with Spring Data JPA repositories.
- Can fetch multiple associations.
- Supports nested associations.
- Better than making everything EAGER.
- Cleaner alternative to JOIN FETCH queries.
- Supports `FETCH` and `LOAD` modes.
- Frequently used in enterprise applications for performance optimization.
- Very important Spring Data JPA interview topic.

---

# Quick Comparison

| Feature | Lazy | Eager | EntityGraph |
|----------|------|--------|------------|
| Default Query Fast | ✅ | ❌ | ✅ |
| Avoid N+1 | ❌ | ✅ | ✅ |
| Flexible | ❌ | ❌ | ✅ |
| Production Friendly | ✅ | ❌ | ✅ |

---

# Interview Shortcut

```java
@EntityGraph(
    attributePaths = {
        "customer",
        "items"
    }
)
List<Order> findAll();
```

Meaning:

```text
Load Orders
      +
Load Customer
      +
Load Items
      ↓
Single SQL Query
      ↓
Avoid N+1 Problem
      ↓
Better Performance
```

This is one of the most commonly used Spring Data JPA techniques for optimizing database access in production applications.