
## Definition

`@LastModifiedDate` is a Spring Data JPA auditing annotation used to automatically store the **date and time when an entity was last updated**.

It tells Spring:

> Update this field with the current timestamp whenever the entity is modified and saved.

Package:

```java
import org.springframework.data.annotation.LastModifiedDate;
```

---

# Why Do We Need @LastModifiedDate?

In enterprise applications, we often need to know:

```text
When was this record last changed?
```

Examples:

```text
Employee Record Update Time
Order Modification Time
User Profile Update Time
Invoice Last Change Time
```

---

Without `@LastModifiedDate`:

```java
employee.setUpdatedAt(
    LocalDateTime.now()
);
```

You would need to manually update this field every time.

---

Problems:

```text
Developer Forgetfulness
Duplicate Code
Inconsistent Data
```

---

Solution:

```java
@LastModifiedDate
```

---

# Basic Example

```java
@Entity

@EntityListeners(
    AuditingEntityListener.class
)
public class Employee {

    @Id

    @GeneratedValue
    private Long id;

    private String name;

    @LastModifiedDate
    private LocalDateTime updatedAt;
}
```

---

When updated:

```java
employeeRepository.save(employee);
```

Spring automatically updates:

```java
updatedAt = LocalDateTime.now()
```

---

# Required Configuration

`@LastModifiedDate` requires Spring Data JPA Auditing.

---

## Step 1

Enable auditing.

```java
@SpringBootApplication

@EnableJpaAuditing
public class Application {
}
```

---

## Step 2

Register auditing listener.

```java
@EntityListeners(
    AuditingEntityListener.class
)
```

---

Without these:

```java
@LastModifiedDate
```

will not work.

---

# Supported Data Types

Most common:

```java
LocalDateTime
```

---

```java
@LastModifiedDate
private LocalDateTime updatedAt;
```

---

Also supports:

```java
LocalDate
```

```java
@LastModifiedDate
private LocalDate updatedDate;
```

---

```java
Instant
```

```java
@LastModifiedDate
private Instant updatedAt;
```

---

```java
Date
```

```java
@LastModifiedDate
private Date updatedAt;
```

---

# Complete Example

## BaseEntity

```java
@MappedSuperclass

@EntityListeners(
    AuditingEntityListener.class
)
public abstract class BaseEntity {

    @CreatedDate
    private LocalDateTime createdAt;

    @LastModifiedDate
    private LocalDateTime updatedAt;
}
```

---

## Employee

```java
@Entity
public class Employee
       extends BaseEntity {

    @Id

    @GeneratedValue
    private Long id;

    private String name;
}
```

---

Generated Table

```sql
EMPLOYEE
(
 ID,
 NAME,
 CREATED_AT,
 UPDATED_AT
)
```

---

# Insert Example

```java
Employee employee =
    new Employee();

employee.setName("Sumit");

employeeRepository.save(employee);
```

---

Automatically:

```text
createdAt = now()
updatedAt = now()
```

---

Database:

| ID | NAME | CREATED_AT | UPDATED_AT |
|----|------|------------|------------|
| 1 | Sumit | 2026-06-27 10:00 | 2026-06-27 10:00 |

---

# Update Example

```java
employee.setName(
    "Sumit Rajput"
);

employeeRepository.save(employee);
```

---

Spring updates:

```java
updatedAt = LocalDateTime.now()
```

---

Database:

| ID | NAME | CREATED_AT | UPDATED_AT |
|----|------|------------|------------|
| 1 | Sumit Rajput | 2026-06-27 10:00 | 2026-06-28 15:30 |

---

Notice:

```text
createdAt unchanged
updatedAt changed
```

---

# Internal Workflow

Save Operation:

```java
repository.save(entity)
```

---

Spring Data JPA:

```text
AuditingEntityListener
            ↓
Detect Entity Update
            ↓
Populate updatedAt
            ↓
Execute SQL UPDATE
```

---

Automatically.

---

# Generated SQL

Update:

```sql
UPDATE employee
SET
    name = 'Sumit Rajput',

    updated_at =
      '2026-06-28 15:30:00'
WHERE id = 1;
```

---

# @CreatedDate vs @LastModifiedDate

Most Asked Interview Question.

---

## @CreatedDate

Tracks:

```text
Creation Time
```

---

Set only once.

---

Never changes.

---

## @LastModifiedDate

Tracks:

```text
Latest Update Time
```

---

Changes on every update.

---

Example:

```java
@CreatedDate
private LocalDateTime createdAt;

@LastModifiedDate
private LocalDateTime updatedAt;
```

---

Comparison:

| Annotation | Insert | Update |
|------------|--------|--------|
| @CreatedDate | ✅ | ❌ |
| @LastModifiedDate | ✅ | ✅ |

---

# Real Enterprise Example

```java
@MappedSuperclass

@EntityListeners(
    AuditingEntityListener.class
)
public abstract class Auditable {

    @CreatedDate
    private LocalDateTime createdAt;

    @LastModifiedDate
    private LocalDateTime updatedAt;

    @CreatedBy
    private String createdBy;

    @LastModifiedBy
    private String modifiedBy;
}
```

---

Used by:

```text
User
Order
Invoice
Employee
Customer
```

---

All entities automatically get audit information.

---

# @LastModifiedDate vs @UpdateTimestamp

Interview Favorite.

---

## Spring Data JPA

```java
@LastModifiedDate
```

Requires:

```java
@EnableJpaAuditing
```

---

Framework:

```text
Spring Data
```

---

## Hibernate

```java
@UpdateTimestamp
```

---

Framework:

```text
Hibernate
```

---

Comparison:

| Feature | @LastModifiedDate | @UpdateTimestamp |
|----------|------------------|------------------|
| Framework | Spring Data | Hibernate |
| Auditing Required | ✅ | ❌ |
| Integrates With CreatedBy | ✅ | ❌ |
| Enterprise Auditing | Excellent | Basic |

---

# Common Mistakes

## Mistake 1

Forgetting EnableJpaAuditing

Wrong:

```java
@SpringBootApplication
```

---

Missing:

```java
@EnableJpaAuditing
```

---

Result:

```java
updatedAt = null
```

---

# Mistake 2

Missing EntityListeners

Wrong:

```java
@Entity
public class Employee {
}
```

---

Need:

```java
@EntityListeners(
    AuditingEntityListener.class
)
```

---

# Mistake 3

Expecting Update Without Save

Example:

```java
employee.setName("Raj");
```

---

Without:

```java
repository.save(employee);
```

---

No update occurs.

---

# Common Interview Questions

## What is @LastModifiedDate?

Automatically stores the last update timestamp.

---

## When is it updated?

Whenever the entity is updated and persisted.

---

## Does it work on insert?

Yes.

Initial value is also set during insert.

---

## What is required?

```java
@EnableJpaAuditing
```

and

```java
@EntityListeners(
 AuditingEntityListener.class
)
```

---

## Difference Between @CreatedDate and @LastModifiedDate?

Creation timestamp vs update timestamp.

---

# Enterprise Best Practice

Create a reusable audit base class.

```java
@MappedSuperclass

@EntityListeners(
    AuditingEntityListener.class
)
public abstract class BaseEntity {

    @CreatedDate
    private LocalDateTime createdAt;

    @LastModifiedDate
    private LocalDateTime updatedAt;
}
```

---

Every entity inherits:

```text
Created Timestamp
Updated Timestamp
```

---

# Key Points To Remember

- `@LastModifiedDate` automatically stores the last modification timestamp.
- Requires Spring Data JPA Auditing.
- Requires `@EnableJpaAuditing`.
- Requires `AuditingEntityListener`.
- Updated during INSERT and UPDATE operations.
- Frequently paired with `@CreatedDate`.
- Supports LocalDateTime, LocalDate, Instant, and Date.
- Commonly used through a `@MappedSuperclass`.
- Widely used in enterprise audit tracking.
- Important Spring Data JPA interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@CreatedDate` | Creation Time |
| `@LastModifiedDate` | Last Update Time |
| `@CreatedBy` | Created By User |
| `@LastModifiedBy` | Updated By User |
| `@EnableJpaAuditing` | Enable Auditing |

---

# Interview Shortcut

```java
@LastModifiedDate
private LocalDateTime updatedAt;
```

Meaning:

```text
Entity Saved
      ↓
Entity Updated
      ↓
Spring Automatically Sets
updatedAt = Current Time
      ↓
Latest Modification Time Stored
```

Typical setup:

```java
@EnableJpaAuditing

@EntityListeners(
    AuditingEntityListener.class
)
```

This is the standard Spring Data JPA mechanism for automatically tracking when a database record was last modified.