
## Definition

`@Version` is a JPA annotation used for **Optimistic Locking**.

It tells Hibernate/JPA:

> Track the version of a database row and prevent concurrent updates from overwriting each other.

Package:

```java
import jakarta.persistence.Version;
```

Prior to Spring Boot 3:

```java
import javax.persistence.Version;
```

---

# Why Do We Need @Version?

Imagine two users editing the same record simultaneously.

---

Database:

```sql
EMPLOYEE
```

| ID | NAME | SALARY |
|----|------|---------|
| 1 | Sumit | 50000 |

---

User A reads:

```text
Salary = 50000
```

---

User B reads:

```text
Salary = 50000
```

---

User A updates:

```text
Salary = 60000
```

Saved successfully.

---

User B updates:

```text
Salary = 70000
```

Saved successfully.

---

Final Result:

```text
Salary = 70000
```

---

Problem:

```text
User A's update is lost.
```

This is called:

```text
Lost Update Problem
```

---

Solution:

```java
@Version
```

---

# What Is Optimistic Locking?

Optimistic Locking assumes:

```text
Conflicts are rare.
```

---

Instead of locking the row:

```text
Track version number
```

and detect conflicts during update.

---

# Basic Syntax

```java
@Version
private Long version;
```

---

Example:

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String name;

    @Version
    private Long version;
}
```

---

Generated Table:

```sql
EMPLOYEE
(
 ID,
 NAME,
 VERSION
)
```

---

# How @Version Works

Initial Row:

| ID | NAME | VERSION |
|----|------|----------|
| 1 | Sumit | 0 |

---

User A loads:

```text
Version = 0
```

---

User B loads:

```text
Version = 0
```

---

User A updates.

Hibernate executes:

```sql
UPDATE employee
SET name='Sumit Updated',
    version=1
WHERE id=1
AND version=0;
```

---

Rows Updated:

```text
1
```

Success.

---

Database:

| ID | NAME | VERSION |
|----|------|----------|
| 1 | Sumit Updated | 1 |

---

User B now updates.

Hibernate executes:

```sql
UPDATE employee
SET name='Another Update',
    version=1
WHERE id=1
AND version=0;
```

---

Rows Updated:

```text
0
```

Because:

```text
Current Version = 1
```

---

Hibernate throws:

```java
OptimisticLockException
```

---

Lost update prevented.

---

# Internal SQL Generated

Entity:

```java
@Entity
public class Employee {

    @Id
    private Long id;

    @Version
    private Long version;
}
```

---

Update SQL:

```sql
UPDATE employee
SET
   name=?,
   version=?
WHERE
   id=?
AND
   version=?;
```

---

Notice:

```sql
version=?
```

inside WHERE clause.

---

That is the key.

---

# Version Increment

Initial:

```text
Version = 0
```

---

After First Update:

```text
Version = 1
```

---

After Second Update:

```text
Version = 2
```

---

After Third Update:

```text
Version = 3
```

---

Hibernate automatically increments it.

---

# Supported Types

Common types:

```java
int
Integer
long
Long
short
Short
Timestamp
```

---

Most common:

```java
@Version
private Long version;
```

---

Or:

```java
@Version
private Integer version;
```

---

# Complete Example

```java
@Entity
public class Product {

    @Id
    private Long id;

    private String name;

    private BigDecimal price;

    @Version
    private Long version;
}
```

---

Database:

| ID | NAME | PRICE | VERSION |
|----|------|--------|----------|
| 1 | Laptop | 50000 | 0 |

---

Each update increases version.

---

# Without @Version

Update:

```java
product.setPrice(
    BigDecimal.valueOf(60000)
);
```

---

Another User:

```java
product.setPrice(
    BigDecimal.valueOf(70000)
);
```

---

Result:

```text
Last Update Wins
```

---

Possible data loss.

---

# With @Version

Result:

```text
Concurrent Update Detected
```

---

Exception thrown.

---

No data loss.

---

# Exception Thrown

Usually:

```java
OptimisticLockException
```

---

Spring wraps it as:

```java
org.springframework.orm.ObjectOptimisticLockingFailureException
```

---

Example:

```java
try {

   repository.save(employee);

}
catch(
 ObjectOptimisticLockingFailureException ex
) {

   // Handle Conflict
}
```

---

# Real World Example

## Online Banking

Balance:

```text
10000
```

---

User A Withdraws:

```text
2000
```

---

User B Withdraws:

```text
5000
```

---

Without @Version:

```text
Wrong Balance Possible
```

---

With @Version:

```text
Conflict Detected
```

---

Application retries.

---

# Real World Example

## E-Commerce Inventory

Stock:

```text
10
```

---

Two customers buy:

```text
10 units simultaneously
```

---

Without Versioning:

```text
Overselling Possible
```

---

With Versioning:

```text
Conflict Detected
```

---

Inventory remains consistent.

---

# @Version vs Pessimistic Locking

Interview Favorite.

---

## Optimistic Locking

Uses:

```java
@Version
```

---

Assumption:

```text
Conflicts Rare
```

---

No database lock.

---

Better performance.

---

## Pessimistic Locking

Uses:

```java
@Lock(
 LockModeType.PESSIMISTIC_WRITE
)
```

---

Assumption:

```text
Conflicts Frequent
```

---

Locks database rows.

---

Comparison:

| Feature | Optimistic | Pessimistic |
|----------|------------|------------|
| Uses Version | ✅ | ❌ |
| DB Lock | ❌ | ✅ |
| Performance | High | Lower |
| Scalability | Better | Lower |

---

# Can We Have Multiple @Version Fields?

No.

---

Wrong:

```java
@Version
private Long version1;

@Version
private Long version2;
```

---

Only one allowed.

---

# Internal Hibernate Processing

Entity:

```java
@Version
private Long version;
```

---

Hibernate Metadata:

```text
Version Column
Version Type
Version Strategy
```

---

During Update:

```text
Read Version
Compare Version
Increment Version
Update Row
```

---

# Common Mistakes

## Mistake 1

Updating Same Row Without Versioning

Results:

```text
Lost Updates
```

---

# Mistake 2

Manually Updating Version

Wrong:

```java
employee.setVersion(100L);
```

---

Hibernate manages it.

---

# Mistake 3

Ignoring OptimisticLockException

Wrong:

```java
repository.save(entity);
```

without conflict handling.

---

Always consider retry/error strategy.

---

# Common Interview Questions

## What is @Version?

Used for optimistic locking.

---

## Why is it needed?

To prevent lost updates.

---

## What problem does it solve?

```text
Concurrent Update Problem
```

---

## How does it work?

Adds version column and checks it during update.

---

## Which exception is thrown?

```java
OptimisticLockException
```

---

## Does it lock database rows?

No.

---

## Can there be multiple @Version fields?

No.

---

## Is version incremented automatically?

Yes.

---

# Real-World Production Example

```java
@Entity
@Table(name = "products")
public class Product {

    @Id

    @GeneratedValue
    private Long id;

    private String name;

    private BigDecimal price;

    @Version
    private Long version;
}
```

---

Generated Table:

```sql
PRODUCTS
(
 ID,
 NAME,
 PRICE,
 VERSION
)
```

---

Update SQL:

```sql
UPDATE products
SET
   price=?,
   version=?
WHERE
   id=?
AND
   version=?;
```

---

Ensures:

```text
No Lost Updates
Concurrent Modification Detection
Safe Data Updates
```

---

# Key Points To Remember

- `@Version` enables optimistic locking.
- Prevents lost updates in concurrent environments.
- Adds a version column to the table.
- Hibernate automatically increments version values.
- Uses version check in UPDATE statements.
- Throws `OptimisticLockException` when conflicts occur.
- No database row locking involved.
- Improves scalability and performance.
- Commonly used in banking, e-commerce, and inventory systems.
- One of the most important JPA interview topics.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Version` | Optimistic Locking |
| `@Id` | Primary Key |
| `@GeneratedValue` | Auto ID Generation |
| `@Column` | Column Mapping |
| `@Lock` | Pessimistic Locking |

---

# Interview Shortcut

```java
@Entity
public class Employee {

    @Id
    private Long id;

    @Version
    private Long version;
}
```

Meaning:

```text
Row Version Tracking
        ↓
Concurrent Update Detection
        ↓
Optimistic Locking
        ↓
No Lost Updates
```

This is the standard JPA mechanism for handling concurrent data modifications safely in enterprise applications.