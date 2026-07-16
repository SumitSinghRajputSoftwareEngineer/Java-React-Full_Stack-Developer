
## Definition

`@CreatedBy` is a Spring Data JPA auditing annotation used to automatically store **who created an entity**.

It tells Spring:

> Populate this field with the current user when the entity is inserted into the database.

Package:

```java
import org.springframework.data.annotation.CreatedBy;
```

---

# Why Do We Need @CreatedBy?

In enterprise applications, we often need to know:

```text
Who created this record?
```

Examples:

```text
Who created an employee?
Who created an order?
Who created an invoice?
Who created a customer?
```

---

Without `@CreatedBy`:

```java
employee.setCreatedBy(
    loggedInUser
);
```

You must manually set the user every time.

---

Problems:

```text
Code Duplication
Human Errors
Missing Audit Information
```

---

Solution:

```java
@CreatedBy
```

---

# Real-World Example

Suppose:

```text
Sumit creates Employee A
Raj creates Employee B
```

Database:

| ID | NAME | CREATED_BY |
|----|------|------------|
| 1 | Amit | Sumit |
| 2 | Rohit | Raj |

---

Automatically maintained using:

```java
@CreatedBy
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

    @CreatedBy
    private String createdBy;
}
```

---

When saved:

```java
employeeRepository.save(employee);
```

---

Spring automatically populates:

```java
createdBy
```

---

# Required Configuration

`@CreatedBy` requires:

## 1. Enable Auditing

```java
@SpringBootApplication

@EnableJpaAuditing(
    auditorAwareRef =
        "auditorProvider"
)
public class Application {
}
```

---

## 2. Entity Listener

```java
@EntityListeners(
    AuditingEntityListener.class
)
```

---

## 3. AuditorAware Implementation

Very Important.

---

# What Is AuditorAware?

Spring needs a way to determine:

```text
Who is the current user?
```

---

Provide implementation:

```java
@Component
public class AuditorProvider
implements AuditorAware<String> {

    @Override
    public Optional<String>
    getCurrentAuditor() {

        return Optional.of(
            "SUMIT"
        );
    }
}
```

---

Whenever an entity is saved:

```java
createdBy = "SUMIT"
```

---

# Complete Example

## Auditor Provider

```java
@Component
public class AuditorProvider
implements AuditorAware<String> {

    @Override
    public Optional<String>
    getCurrentAuditor() {

        return Optional.of(
            "SUMIT"
        );
    }
}
```

---

## Configuration

```java
@EnableJpaAuditing(
    auditorAwareRef =
        "auditorProvider"
)
```

---

## Entity

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

    @CreatedBy
    private String createdBy;
}
```

---

## Save

```java
employeeRepository.save(
    employee
);
```

---

Database:

| ID | NAME | CREATED_BY |
|----|------|------------|
| 1 | Sumit | SUMIT |

---

Automatically populated.

---

# Production Example With Spring Security

Usually:

```java
SecurityContextHolder
```

is used.

---

```java
@Component
public class AuditorProvider
implements AuditorAware<String> {

    @Override
    public Optional<String>
    getCurrentAuditor() {

        Authentication auth =
            SecurityContextHolder
                .getContext()
                .getAuthentication();

        if(auth == null) {
            return Optional.empty();
        }

        return Optional.of(
            auth.getName()
        );
    }
}
```

---

Current logged-in user:

```text
sumit.rajput
```

---

Stored automatically.

---

# Database Example

User:

```text
sumit.rajput
```

creates employee.

---

Database:

| ID | NAME | CREATED_BY |
|----|------|------------|
| 1 | Amit | sumit.rajput |

---

Later:

```text
raj.kumar
```

creates another employee.

---

Database:

| ID | NAME | CREATED_BY |
|----|------|------------|
| 2 | Rohit | raj.kumar |

---

# What Happens During Update?

Suppose:

```java
employee.setName(
    "Amit Sharma"
);
```

---

Save again.

---

Result:

```text
createdBy remains unchanged
```

---

Important:

```java
@CreatedBy
```

is populated only once.

---

# @CreatedBy vs @LastModifiedBy

Interview Favorite.

---

## @CreatedBy

Tracks:

```text
Who created the entity?
```

---

Set once.

---

Never changes.

---

## @LastModifiedBy

Tracks:

```text
Who last updated the entity?
```

---

Changes on every update.

---

Example:

```java
@CreatedBy
private String createdBy;

@LastModifiedBy
private String updatedBy;
```

---

Comparison:

| Annotation | Insert | Update |
|------------|--------|--------|
| @CreatedBy | ✅ | ❌ |
| @LastModifiedBy | ✅ | ✅ |

---

# Complete Enterprise Audit Class

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

    @CreatedBy
    private String createdBy;

    @LastModifiedBy
    private String modifiedBy;
}
```

---

Every entity gets:

```text
Created Time
Updated Time
Created By
Updated By
```

---

# Internal Workflow

Entity Save:

```java
repository.save(entity);
```

---

Spring:

```text
AuditingEntityListener
          ↓
Calls AuditorAware
          ↓
Gets Current User
          ↓
Sets createdBy
          ↓
Insert Entity
```

---

Automatically.

---

# Generated SQL

Insert:

```sql
INSERT INTO employee
(
 name,
 created_by
)
VALUES
(
 'Amit',
 'sumit.rajput'
)
```

---

Value generated automatically.

---

# Common Mistakes

## Mistake 1

Missing AuditorAware

Wrong:

```java
@CreatedBy
private String createdBy;
```

---

Without:

```java
AuditorAware
```

---

Result:

```java
createdBy = null
```

---

# Mistake 2

Missing EnableJpaAuditing

Wrong:

```java
@SpringBootApplication
```

---

Need:

```java
@EnableJpaAuditing(
 auditorAwareRef =
 "auditorProvider"
)
```

---

# Mistake 3

Missing EntityListeners

Need:

```java
@EntityListeners(
 AuditingEntityListener.class
)
```

---

Otherwise auditing won't execute.

---

# Common Interview Questions

## What is @CreatedBy?

Stores the user who created the entity.

---

## Does it update on modification?

No.

Only populated during insert.

---

## What is required?

```java
@EnableJpaAuditing
```

and

```java
AuditorAware<T>
```

---

## How does Spring determine the user?

Through:

```java
AuditorAware<T>
```

implementation.

---

## Can it work with Spring Security?

Yes.

Usually via:

```java
SecurityContextHolder
```

---

# Enterprise Best Practice

Create a reusable audit class.

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
Employee
Order
Invoice
Customer
Product
```

---

# Key Points To Remember

- `@CreatedBy` stores who created an entity.
- Requires Spring Data JPA Auditing.
- Requires `@EnableJpaAuditing`.
- Requires `AuditorAware<T>`.
- Usually integrated with Spring Security.
- Populated only during INSERT.
- Does not change on UPDATE.
- Frequently used with `@CreatedDate`.
- Commonly placed in a `@MappedSuperclass`.
- Important Spring Boot auditing interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@CreatedDate` | Creation Timestamp |
| `@LastModifiedDate` | Update Timestamp |
| `@CreatedBy` | Creator User |
| `@LastModifiedBy` | Last Modifier User |
| `@EnableJpaAuditing` | Enable Auditing |

---

# Interview Shortcut

```java
@CreatedBy
private String createdBy;
```

Meaning:

```text
Entity Created
       ↓
Spring Calls AuditorAware
       ↓
Gets Current User
       ↓
Stores User Name
       ↓
Value Never Changes Again
```

Typical setup:

```java
@EnableJpaAuditing(
 auditorAwareRef =
 "auditorProvider"
)
```

```java
@Component
class AuditorProvider
implements AuditorAware<String>
```

This is the standard Spring Data JPA auditing mechanism for automatically tracking who originally created a database record.