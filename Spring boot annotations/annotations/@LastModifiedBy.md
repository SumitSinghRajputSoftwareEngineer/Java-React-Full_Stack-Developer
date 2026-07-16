
## Definition

`@LastModifiedBy` is a Spring Data JPA auditing annotation used to automatically store **who last modified an entity**.

It tells Spring:

> Populate this field with the current user whenever the entity is created or updated.

Package:

```java
import org.springframework.data.annotation.LastModifiedBy;
```

---

# Why Do We Need @LastModifiedBy?

In enterprise applications, we often need to know:

```text
Who last changed this record?
```

Examples:

```text
Who updated an employee record?
Who modified an order?
Who changed a customer's address?
Who edited an invoice?
```

---

Without `@LastModifiedBy`:

```java
employee.setModifiedBy(
    loggedInUser
);
```

You would have to do this manually on every update.

---

Problems:

```text
Code Duplication
Missed Updates
Audit Inconsistencies
```

---

Solution:

```java
@LastModifiedBy
```

---

# Real-World Example

Suppose:

```text
Sumit creates Employee A
Raj updates Employee A
```

Database:

| ID | NAME | CREATED_BY | MODIFIED_BY |
|----|------|------------|-------------|
| 1 | Amit | Sumit | Raj |

---

`@LastModifiedBy` tracks:

```text
Most Recent User
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

    @LastModifiedBy
    private String modifiedBy;
}
```

---

When updated:

```java
employeeRepository.save(employee);
```

---

Spring automatically sets:

```java
modifiedBy
```

---

# Required Configuration

## Step 1

Enable JPA Auditing.

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

## Step 2

Register Entity Listener.

```java
@EntityListeners(
    AuditingEntityListener.class
)
```

---

## Step 3

Provide AuditorAware

Spring must know:

```text
Who is the current user?
```

---

# AuditorAware Example

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
modifiedBy = "SUMIT"
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

    @LastModifiedBy
    private String modifiedBy;
}
```

---

# Insert Example

```java
Employee employee =
    new Employee();

employee.setName("Amit");

employeeRepository.save(
    employee
);
```

---

Database:

| ID | NAME | MODIFIED_BY |
|----|------|-------------|
| 1 | Amit | SUMIT |

---

Even during insert, value is populated.

---

# Update Example

User:

```text
RAJ
```

updates record.

---

Database becomes:

| ID | NAME | MODIFIED_BY |
|----|------|-------------|
| 1 | Amit Sharma | RAJ |

---

Notice:

```text
Latest User Replaces Previous User
```

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

Current user:

```text
sumit.rajput
```

---

Automatically stored.

---

# @CreatedBy vs @LastModifiedBy

Most Asked Interview Question.

---

## @CreatedBy

Tracks:

```text
Who Created Record
```

---

Set only once.

---

Never changes.

---

## @LastModifiedBy

Tracks:

```text
Who Last Updated Record
```

---

Changes every update.

---

Example:

```java
@CreatedBy
private String createdBy;

@LastModifiedBy
private String modifiedBy;
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
Modified By
```

---

# Database Example

Initial Insert:

User:

```text
SUMIT
```

---

Database:

| ID | NAME | CREATED_BY | MODIFIED_BY |
|----|------|------------|-------------|
| 1 | Amit | SUMIT | SUMIT |

---

Update:

User:

```text
RAJ
```

---

Database:

| ID | NAME | CREATED_BY | MODIFIED_BY |
|----|------|------------|-------------|
| 1 | Amit Sharma | SUMIT | RAJ |

---

Notice:

```text
CreatedBy remains same
ModifiedBy changes
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
Sets modifiedBy
          ↓
Update Entity
```

---

Automatically.

---

# Generated SQL

Update:

```sql
UPDATE employee
SET
    name = 'Amit Sharma',

    modified_by = 'RAJ'
WHERE id = 1;
```

---

Value generated automatically.

---

# Common Mistakes

## Mistake 1

Missing AuditorAware

Wrong:

```java
@LastModifiedBy
private String modifiedBy;
```

---

Without:

```java
AuditorAware<T>
```

---

Result:

```java
modifiedBy = null
```

---

# Mistake 2

Missing EnableJpaAuditing

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

Otherwise auditing won't run.

---

# Common Interview Questions

## What is @LastModifiedBy?

Stores the user who last modified an entity.

---

## When is it updated?

During INSERT and UPDATE operations.

---

## How does Spring determine the user?

Through:

```java
AuditorAware<T>
```

---

## Can it work with Spring Security?

Yes.

Usually via:

```java
SecurityContextHolder
```

---

## Difference Between @CreatedBy and @LastModifiedBy?

Original creator vs latest modifier.

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

- `@LastModifiedBy` stores the user who last changed the entity.
- Requires Spring Data JPA Auditing.
- Requires `@EnableJpaAuditing`.
- Requires `AuditorAware<T>`.
- Usually integrated with Spring Security.
- Updated during INSERT and UPDATE.
- Changes whenever another user modifies the entity.
- Frequently paired with `@CreatedBy`.
- Commonly placed in a `@MappedSuperclass`.
- Important Spring Boot auditing interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@CreatedDate` | Creation Time |
| `@LastModifiedDate` | Last Update Time |
| `@CreatedBy` | Creator User |
| `@LastModifiedBy` | Last Modifier User |
| `@EnableJpaAuditing` | Enable Auditing |

---

# Interview Shortcut

```java
@LastModifiedBy
private String modifiedBy;
```

Meaning:

```text
Entity Saved/Updated
         ↓
Spring Calls AuditorAware
         ↓
Gets Current User
         ↓
Stores User Name
         ↓
Value Updated Every Modification
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

This is the standard Spring Data JPA auditing mechanism for automatically tracking who most recently modified a database record.