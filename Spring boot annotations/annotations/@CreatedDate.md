
## Definition

`@CreatedDate` is a Spring Data JPA auditing annotation used to automatically store the **date and time when an entity is first created**.

It tells Spring:

> Populate this field with the current timestamp when the entity is inserted into the database.

Package:

```java
import org.springframework.data.annotation.CreatedDate;
```

---

# Why Do We Need @CreatedDate?

Almost every enterprise application needs:

```text
When was this record created?
```

Examples:

```text
User Registration Date
Order Creation Date
Employee Joining Record Date
Invoice Creation Date
```

---

Without `@CreatedDate`:

```java
employee.setCreatedAt(
    LocalDateTime.now()
);
```

You must do this manually everywhere.

---

Problems:

```text
Code Duplication
Human Errors
Inconsistent Data
```

---

Solution:

```java
@CreatedDate
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

    @CreatedDate
    private LocalDateTime createdAt;
}
```

---

When saved:

```java
employeeRepository.save(employee);
```

---

Spring automatically sets:

```java
createdAt = LocalDateTime.now()
```

---

# Required Configuration

`@CreatedDate` will NOT work by itself.

You must enable JPA Auditing.

---

## Step 1

```java
@SpringBootApplication

@EnableJpaAuditing
public class Application {
}
```

---

## Step 2

```java
@EntityListeners(
    AuditingEntityListener.class
)
```

on entity or base entity.

---

Without these:

```java
@CreatedDate
```

does nothing.

---

# Supported Data Types

Most common:

```java
LocalDateTime
```

---

```java
@CreatedDate
private LocalDateTime createdAt;
```

---

Also supports:

```java
LocalDate
```

```java
@CreatedDate
private LocalDate createdDate;
```

---

```java
Instant
```

```java
@CreatedDate
private Instant createdAt;
```

---

```java
Date
```

```java
@CreatedDate
private Date createdAt;
```

---

# Complete Example

## Base Entity

```java
@MappedSuperclass

@EntityListeners(
    AuditingEntityListener.class
)
public abstract class BaseEntity {

    @CreatedDate
    private LocalDateTime createdAt;
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
 CREATED_AT
)
```

---

# What Happens During Insert?

Code:

```java
Employee employee =
    new Employee();

employee.setName("Sumit");

employeeRepository.save(employee);
```

---

Before Insert:

```java
createdAt = null
```

---

Spring Auditing:

```java
createdAt =
 LocalDateTime.now()
```

---

Generated SQL:

```sql
INSERT INTO employee
(
 name,
 created_at
)
VALUES
(
 'Sumit',
 '2026-06-27 18:20:00'
)
```

---

# What Happens During Update?

Example:

```java
employee.setName(
    "Sumit Rajput"
);

employeeRepository.save(employee);
```

---

Result:

```text
createdAt remains unchanged
```

---

Important:

```java
@CreatedDate
```

is set only once.

---

# Database Example

After Insert:

| ID | NAME | CREATED_AT |
|----|------|------------|
| 1 | Sumit | 2026-06-27 10:00 |

---

After Update:

| ID | NAME | CREATED_AT |
|----|------|------------|
| 1 | Sumit Rajput | 2026-06-27 10:00 |

---

Notice:

```text
CREATED_AT unchanged
```

---

# @CreatedDate vs @LastModifiedDate

Interview Favorite.

---

## @CreatedDate

Tracks:

```text
Insert Time
```

---

Set once.

---

Never changes.

---

## @LastModifiedDate

Tracks:

```text
Last Update Time
```

---

Updated on every modification.

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

| Annotation | Updated On |
|------------|------------|
| @CreatedDate | INSERT |
| @LastModifiedDate | UPDATE |

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

All entities inherit:

```text
Created Date
Updated Date
Created By
Modified By
```

---

# Internal Workflow

Application Startup:

```java
@EnableJpaAuditing
```

---

Registers:

```text
Auditing Infrastructure
```

---

Entity Save:

```java
save(entity)
```

---

Spring executes:

```text
AuditingEntityListener
          ↓
Populate CreatedDate
          ↓
Persist Entity
```

---

# Generated SQL Example

Entity:

```java
@CreatedDate
private LocalDateTime createdAt;
```

---

Insert:

```sql
INSERT INTO employee
(
 name,
 created_at
)
VALUES
(
 'Sumit',
 CURRENT_TIMESTAMP
)
```

---

Value automatically generated.

---

# @CreatedDate vs @CreationTimestamp

Interview Favorite.

---

## Spring Data JPA

```java
@CreatedDate
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
@CreationTimestamp
```

---

No auditing required.

---

Framework:

```text
Hibernate
```

---

Comparison:

| Feature | @CreatedDate | @CreationTimestamp |
|----------|-------------|-------------------|
| Framework | Spring Data | Hibernate |
| Auditing Required | ✅ | ❌ |
| Works With CreatedBy | ✅ | ❌ |
| Enterprise Auditing | Excellent | Limited |

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
createdAt = null
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

Expecting Updates

Wrong expectation:

```text
createdAt changes after update
```

---

Never happens.

---

Only insert operation sets value.

---

# Common Interview Questions

## What is @CreatedDate?

Automatically stores entity creation timestamp.

---

## Does it update automatically?

No.

Only on insert.

---

## What is required for it to work?

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

## Which data types are supported?

```java
LocalDateTime
LocalDate
Instant
Date
```

---

## Difference Between @CreatedDate and @LastModifiedDate?

Insert timestamp vs update timestamp.

---

# Enterprise Best Practice

Create:

```java
BaseEntity
```

---

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

Every entity inherits audit fields.

---

# Key Points To Remember

- `@CreatedDate` automatically stores creation timestamp.
- Works only with Spring Data JPA Auditing.
- Requires `@EnableJpaAuditing`.
- Requires `AuditingEntityListener`.
- Populated only during INSERT.
- Never changes during UPDATE.
- Commonly used with `@MappedSuperclass`.
- Supports LocalDateTime, LocalDate, Instant, and Date.
- Frequently used in enterprise auditing systems.
- Important Spring Data JPA interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@CreatedDate` | Creation Time |
| `@LastModifiedDate` | Update Time |
| `@CreatedBy` | Creator |
| `@LastModifiedBy` | Modifier |
| `@EnableJpaAuditing` | Enable Auditing |

---

# Interview Shortcut

```java
@CreatedDate
private LocalDateTime createdAt;
```

Meaning:

```text
Entity Inserted
        ↓
Spring Automatically Sets
createdAt = Current Time
        ↓
Value Never Changes Again
```

Typical setup:

```java
@EnableJpaAuditing

@EntityListeners(
    AuditingEntityListener.class
)
```

This is the standard Spring Data JPA approach for automatically tracking when a database record was originally created.