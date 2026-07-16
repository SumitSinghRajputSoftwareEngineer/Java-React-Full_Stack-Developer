
## Definition

`@Lock` is a Spring Data JPA annotation used to specify the **locking strategy** when retrieving entities from the database.

It tells JPA/Hibernate:

> Apply a lock while reading data to prevent concurrency issues.

Package:

```java
import org.springframework.data.jpa.repository.Lock;
```

Lock types come from:

```java
import jakarta.persistence.LockModeType;
```

---

# Why Do We Need @Lock?

Imagine:

```text
User A reads Account Balance = 1000
User B reads Account Balance = 1000
```

---

User A withdraws:

```text
-500
```

Balance:

```text
500
```

---

User B withdraws:

```text
-700
```

Balance:

```text
-200
```

---

Problem:

```text
Race Condition
Lost Update
Data Corruption
```

---

Solution:

```java
@Lock
```

---

# Real Banking Example

Account:

```text
Balance = 1000
```

---

Two users transfer money simultaneously.

Without locking:

```text
Both Read Same Balance
Both Update
Incorrect Result
```

---

With locking:

```text
One Transaction Waits
```

---

Data remains consistent.

---

# Basic Syntax

```java
@Lock(
    LockModeType.PESSIMISTIC_WRITE
)
```

Example:

```java
@Repository
public interface AccountRepository
extends JpaRepository<Account, Long> {

    @Lock(
      LockModeType.PESSIMISTIC_WRITE
    )
    Optional<Account>
    findById(Long id);
}
```

---

# Available Lock Types

Very Important Interview Topic.

---

## OPTIMISTIC

```java
LockModeType.OPTIMISTIC
```

---

Uses:

```java
@Version
```

field.

---

No database lock.

---

Checks version during update.

---

Example:

```java
@Version
private Long version;
```

---

# OPTIMISTIC_FORCE_INCREMENT

```java
LockModeType
    .OPTIMISTIC_FORCE_INCREMENT
```

---

Updates version even if entity unchanged.

---

Used in advanced scenarios.

---

# PESSIMISTIC_READ

```java
LockModeType.PESSIMISTIC_READ
```

---

Database lock.

---

Allows:

```text
Multiple Reads
```

---

Blocks:

```text
Writes
```

---

# PESSIMISTIC_WRITE

```java
LockModeType.PESSIMISTIC_WRITE
```

---

Database lock.

---

Blocks:

```text
Reads (depending on DB)
Writes
```

---

Most commonly used.

---

# PESSIMISTIC_FORCE_INCREMENT

```java
LockModeType
    .PESSIMISTIC_FORCE_INCREMENT
```

---

Combination:

```text
Write Lock
+
Version Increment
```

---

Used rarely.

---

# Optimistic Locking

Most popular in enterprise applications.

---

Entity:

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

Repository:

```java
@Lock(
 LockModeType.OPTIMISTIC
)
Optional<Employee>
findById(Long id);
```

---

How It Works

Transaction A:

```text
Version = 1
```

---

Transaction B:

```text
Version = 1
```

---

A updates.

Version:

```text
1 → 2
```

---

B updates.

Expected:

```text
Version = 1
```

Actual:

```text
Version = 2
```

---

Hibernate throws:

```java
OptimisticLockException
```

---

Prevents lost update.

---

# Pessimistic Locking

Database-level locking.

---

Repository:

```java
@Lock(
 LockModeType.PESSIMISTIC_WRITE
)
Optional<Employee>
findById(Long id);
```

---

Generated SQL:

```sql
SELECT *
FROM employee
WHERE id=?
FOR UPDATE
```

---

Database locks row.

---

Other transactions wait.

---

# Example

Account:

```text
Balance = 1000
```

---

Transaction A:

```java
findById()
```

using:

```java
PESSIMISTIC_WRITE
```

---

Database:

```text
Row Locked
```

---

Transaction B:

```java
findById()
```

---

Waits until:

```text
Transaction A commits
```

---

# Optimistic vs Pessimistic

Most Asked Interview Question.

---

## Optimistic Locking

Assumption:

```text
Conflicts Are Rare
```

---

No DB lock.

---

Uses:

```java
@Version
```

---

Better performance.

---

## Pessimistic Locking

Assumption:

```text
Conflicts Are Frequent
```

---

Uses DB lock.

---

Slower.

---

Stronger consistency.

---

Comparison:

| Feature | Optimistic | Pessimistic |
|----------|------------|------------|
| DB Lock | ❌ | ✅ |
| @Version | ✅ | Optional |
| Performance | Better | Lower |
| Scalability | Better | Lower |
| Consistency | Good | Strong |
| Deadlock Risk | ❌ | ✅ |

---

# Example With Custom Query

```java
@Lock(
 LockModeType.PESSIMISTIC_WRITE
)

@Query("""
SELECT e
FROM Employee e
WHERE e.id=:id
""")
Optional<Employee>
findLockedEmployee(
    Long id
);
```

---

Lock applied to query result.

---

# Transaction Requirement

Important.

---

Locks work correctly only inside:

```java
@Transactional
```

---

Example:

```java
@Transactional
public void updateEmployee(
        Long id) {

    Employee emp =
        repository
        .findById(id)
        .orElseThrow();

    emp.setSalary(10000);
}
```

---

Without transaction:

```text
Lock released immediately
```

---

No benefit.

---

# Internal Workflow

Repository Method:

```java
findById(id)
```

---

Spring Data:

```text
Read @Lock
       ↓
Apply Lock Mode
       ↓
Execute Query
       ↓
Database Acquires Lock
       ↓
Return Entity
```

---

# Real Production Example

Inventory System.

---

Product Quantity:

```text
10
```

---

Two customers order:

```text
8 Units
8 Units
```

---

Without lock:

```text
Overselling
```

---

With:

```java
PESSIMISTIC_WRITE
```

---

Only one order processes at a time.

---

Inventory remains correct.

---

# Generated SQL

Pessimistic Write:

```sql
SELECT *
FROM employee
WHERE id=1
FOR UPDATE
```

---

Oracle:

```sql
FOR UPDATE
```

---

MySQL:

```sql
FOR UPDATE
```

---

PostgreSQL:

```sql
FOR UPDATE
```

---

Hibernate generates vendor-specific SQL.

---

# Common Exceptions

## OptimisticLockException

Occurs when:

```java
@Version
```

value changes unexpectedly.

---

Example:

```text
Version mismatch
```

---

# PessimisticLockException

Occurs when:

```text
Unable to acquire lock
```

---

Example:

```text
Timeout
Deadlock
```

---

# Common Mistakes

## Mistake 1

Using Optimistic Lock Without @Version

Wrong:

```java
@Lock(
 OPTIMISTIC
)
```

---

Without:

```java
@Version
```

---

No proper version checking.

---

# Mistake 2

Using Lock Without Transaction

Wrong:

```java
findById()
```

---

Without:

```java
@Transactional
```

---

Lock released immediately.

---

# Mistake 3

Using Pessimistic Lock Everywhere

Problem:

```text
Poor Performance
Deadlocks
Blocking
```

---

Use only when necessary.

---

# Common Interview Questions

## What is @Lock?

Specifies locking strategy for repository queries.

---

## Why use locking?

Prevent:

```text
Lost Updates
Race Conditions
Concurrency Issues
```

---

## Difference Between Optimistic and Pessimistic Locking?

Version checking vs database locking.

---

## Which is preferred?

Usually:

```text
Optimistic Locking
```

because it scales better.

---

## What is required for Optimistic Locking?

```java
@Version
```

---

## Does Pessimistic Lock Use Database Locks?

Yes.

---

# Enterprise Best Practice

Use:

```java
OPTIMISTIC
```

for:

```text
Most Business Applications
```

---

Use:

```java
PESSIMISTIC_WRITE
```

for:

```text
Banking
Inventory
Financial Transactions
Critical Updates
```

---

Avoid unnecessary locking.

---

# Key Points To Remember

- `@Lock` specifies entity locking strategy.
- Used on repository methods.
- Supports optimistic and pessimistic locking.
- Optimistic locking requires `@Version`.
- Pessimistic locking uses database row locks.
- Prevents race conditions and lost updates.
- Works best inside transactions.
- Essential for concurrent applications.
- Frequently used in banking and inventory systems.
- Important Spring Data JPA interview topic.

---

# Quick Comparison

| Lock Type | Description |
|------------|------------|
| `OPTIMISTIC` | Version Checking |
| `OPTIMISTIC_FORCE_INCREMENT` | Version Increment |
| `PESSIMISTIC_READ` | Read Lock |
| `PESSIMISTIC_WRITE` | Write Lock |
| `PESSIMISTIC_FORCE_INCREMENT` | Write Lock + Version Update |

---

# Interview Shortcut

```java
@Lock(
 LockModeType.PESSIMISTIC_WRITE
)
Optional<Employee>
findById(Long id);
```

Meaning:

```text
Read Entity
      ↓
Acquire Database Lock
      ↓
Prevent Concurrent Updates
      ↓
Release Lock After Commit
```

Most common production usage:

```java
@Lock(
 LockModeType.OPTIMISTIC
)
```

with:

```java
@Version
private Long version;
```

This is the standard JPA mechanism for handling concurrent access and preventing data inconsistency in multi-user applications.