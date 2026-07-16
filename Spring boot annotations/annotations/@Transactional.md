
## Definition

`@Transactional` is a Spring annotation used to define a **transaction boundary** around a method or class.

It tells Spring:

> Execute all database operations within a single transaction. If everything succeeds, commit the transaction; if any failure occurs, roll it back.

Package:

```java
import org.springframework.transaction.annotation.Transactional;
```

---

# What is a Transaction?

A transaction is a group of operations treated as a single unit of work.

Example:

```text
Withdraw ₹1000 from Account A
Deposit ₹1000 into Account B
```

---

Both operations must succeed together.

---

If only withdrawal succeeds:

```text
Money Lost
```

---

If both succeed:

```text
Money Transferred Successfully
```

---

This is exactly what transactions guarantee.

---

# Why Do We Need @Transactional?

Without transaction:

```java
accountA.withdraw(1000);

accountB.deposit(1000);
```

---

Suppose:

```java
accountA.withdraw(1000);
```

succeeds.

---

Then:

```java
accountB.deposit(1000);
```

fails.

---

Result:

```text
Account A Lost Money
Account B Didn't Receive Money
```

---

Database becomes inconsistent.

---

Solution:

```java
@Transactional
```

---

# Basic Example

```java
@Service
public class AccountService {

    @Transactional
    public void transferMoney(
            Long fromId,
            Long toId,
            Double amount) {

        Account from =
            repository.findById(fromId)
                      .orElseThrow();

        Account to =
            repository.findById(toId)
                      .orElseThrow();

        from.withdraw(amount);

        to.deposit(amount);
    }
}
```

---

If everything succeeds:

```text
COMMIT
```

---

If exception occurs:

```text
ROLLBACK
```

---

# ACID Properties

Most Important Interview Topic.

---

## A → Atomicity

All or nothing.

---

Example:

```text
Withdraw + Deposit
```

---

Either:

```text
Both Succeed
```

or

```text
Both Fail
```

---

Never partial execution.

---

## C → Consistency

Database always remains valid.

---

Example:

```text
Balance cannot become invalid
```

---

## I → Isolation

Transactions should not interfere with each other.

---

Example:

```text
User A Transaction
User B Transaction
```

---

Must not corrupt each other's data.

---

## D → Durability

Committed data survives crashes.

---

Example:

```text
Server Restart
```

---

Data remains saved.

---

# How Spring Transaction Works

Method call:

```java
service.transferMoney()
```

---

Spring creates a proxy:

```text
Proxy
   ↓
Start Transaction
   ↓
Execute Method
   ↓
Commit/Rollback
```

---

Internally:

```java
Connection.setAutoCommit(false);
```

---

# Successful Transaction

```java
@Transactional
public void saveEmployee() {

    repository.save(emp1);

    repository.save(emp2);
}
```

---

Generated:

```sql
INSERT ...
INSERT ...
COMMIT
```

---

Both saved.

---

# Failed Transaction

```java
@Transactional
public void saveEmployee() {

    repository.save(emp1);

    int x = 10 / 0;

    repository.save(emp2);
}
```

---

Exception:

```java
ArithmeticException
```

---

Generated:

```sql
INSERT ...
ROLLBACK
```

---

Nothing saved.

---

# Transaction at Method Level

```java
@Transactional
public void save() {
}
```

---

Applies only to that method.

---

# Transaction at Class Level

```java
@Service

@Transactional
public class EmployeeService {
}
```

---

Applies to all public methods.

---

# Read Only Transactions

Optimization.

---

```java
@Transactional(
    readOnly = true
)
public List<Employee> getEmployees() {
}
```

---

Benefits:

```text
Better Performance
Less Locking
Hibernate Optimization
```

---

Use for:

```text
SELECT Queries
```

---

# Rollback Rules

Default Behavior:

```text
Rollback only for RuntimeException
```

---

Rollback:

```java
NullPointerException
ArithmeticException
IllegalArgumentException
```

---

Not rollback:

```java
IOException
SQLException
```

because they are checked exceptions.

---

# Rollback For Checked Exception

```java
@Transactional(
    rollbackFor =
    Exception.class
)
```

---

Example:

```java
@Transactional(
 rollbackFor =
 Exception.class
)
public void process()
throws Exception {

}
```

---

Now:

```java
IOException
```

also triggers rollback.

---

# noRollbackFor

```java
@Transactional(
 noRollbackFor =
 ArithmeticException.class
)
```

---

Even if exception occurs:

```text
Transaction Commits
```

---

Rarely used.

---

# Propagation

Very Important Interview Topic.

---

Defines:

```text
How Transactions Interact
```

---

# REQUIRED (Default)

```java
@Transactional(
 propagation =
 Propagation.REQUIRED
)
```

---

Behavior:

```text
Join Existing Transaction
OR
Create New One
```

---

Most common.

---

# REQUIRES_NEW

```java
@Transactional(
 propagation =
 Propagation.REQUIRES_NEW
)
```

---

Behavior:

```text
Suspend Current Transaction
Create New Transaction
```

---

Example:

```java
Save Audit Log
```

even if main transaction fails.

---

# SUPPORTS

```java
@Transactional(
 propagation =
 Propagation.SUPPORTS
)
```

---

Behavior:

```text
Use Existing Transaction
Otherwise Execute Without Transaction
```

---

# NOT_SUPPORTED

```java
@Transactional(
 propagation =
 Propagation.NOT_SUPPORTED
)
```

---

Behavior:

```text
Execute Without Transaction
```

---

# MANDATORY

```java
@Transactional(
 propagation =
 Propagation.MANDATORY
)
```

---

Behavior:

```text
Must Have Existing Transaction
```

---

Otherwise:

```java
IllegalTransactionStateException
```

---

# NEVER

```java
@Transactional(
 propagation =
 Propagation.NEVER
)
```

---

Behavior:

```text
Must Not Run In Transaction
```

---

# NESTED

```java
@Transactional(
 propagation =
 Propagation.NESTED
)
```

---

Uses:

```text
Savepoints
```

---

Rare.

---

# Isolation Levels

Control concurrent access.

---

## READ_UNCOMMITTED

Lowest isolation.

---

Can read:

```text
Uncommitted Data
```

---

Dirty reads possible.

---

# READ_COMMITTED

Most commonly used.

---

Can read only:

```text
Committed Data
```

---

# REPEATABLE_READ

Same row returns same value.

---

Prevents:

```text
Non-repeatable Reads
```

---

# SERIALIZABLE

Highest isolation.

---

Transactions run almost sequentially.

---

Strong consistency.

---

Lower performance.

---

# Example

```java
@Transactional(
 isolation =
 Isolation.REPEATABLE_READ
)
```

---

# Transaction Manager

Spring uses:

```java
PlatformTransactionManager
```

---

Examples:

```java
JpaTransactionManager
DataSourceTransactionManager
```

---

# Internal Workflow

Method Call:

```java
transferMoney()
```

---

Spring Proxy:

```text
Begin Transaction
        ↓
Execute Method
        ↓
Exception?
      /    \
    Yes    No
     ↓      ↓
 Rollback Commit
```

---

# Common Mistakes

## Mistake 1

Private Method

Wrong:

```java
@Transactional
private void save() {
}
```

---

Spring AOP cannot intercept.

---

No transaction created.

---

# Mistake 2

Self Invocation

```java
public void methodA() {
    methodB();
}
```

---

```java
@Transactional
public void methodB() {
}
```

---

Transaction not applied.

---

Because proxy is bypassed.

---

# Mistake 3

Expecting Checked Exception Rollback

Wrong assumption.

---

Need:

```java
rollbackFor
```

---

# Common Interview Questions

## What is @Transactional?

Defines transaction boundaries around methods.

---

## What Happens When Exception Occurs?

Rollback.

---

## Which Exceptions Trigger Rollback?

By default:

```java
RuntimeException
Error
```

---

## What is Propagation?

Defines interaction between transactions.

---

## Most Common Propagation?

```java
REQUIRED
```

---

## What is Isolation?

Controls visibility between concurrent transactions.

---

## Most Common Isolation?

```java
READ_COMMITTED
```

---

## Why Doesn't @Transactional Work On Private Methods?

Spring AOP proxy cannot intercept private methods.

---

# Enterprise Best Practice

Use:

```java
@Service
@Transactional
```

for business logic.

---

Use:

```java
@Transactional(readOnly = true)
```

for read operations.

---

Keep transactions:

```text
Short
Fast
Focused
```

---

Avoid:

```text
External API Calls
Long Loops
File Uploads
```

inside transactions.

---

# Key Points To Remember

- `@Transactional` manages database transactions.
- Ensures ACID properties.
- Commits on success.
- Rolls back on failure.
- Implemented using Spring AOP proxies.
- Supports propagation and isolation levels.
- Default rollback is for RuntimeException.
- Can be applied at method or class level.
- Read-only transactions improve performance.
- One of the most important Spring annotations.

---

# Quick Comparison

| Attribute | Purpose |
|------------|----------|
| `readOnly=true` | Read Optimization |
| `rollbackFor` | Rollback Checked Exceptions |
| `noRollbackFor` | Prevent Rollback |
| `propagation` | Transaction Behavior |
| `isolation` | Concurrency Control |

---

# Interview Shortcut

```java
@Transactional
public void transferMoney() {
}
```

Meaning:

```text
Start Transaction
       ↓
Execute Business Logic
       ↓
Success ?
   /       \
 Yes       No
  ↓         ↓
Commit   Rollback
```

Most common production usage:

```java
@Service

@Transactional
public class EmployeeService {
}
```

This is the standard Spring Framework mechanism for ensuring data consistency, atomic operations, and reliable database transactions across business operations.