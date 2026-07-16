
## Definition

`@EnableJpaAuditing` is a Spring Data JPA annotation that enables **automatic auditing** of entities.

It tells Spring:

> Automatically populate fields such as created date, modified date, created by, and modified by whenever an entity is saved or updated.

Package:

```java
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;
```

---

# What Is Auditing?

Auditing means tracking:

```text
Who created a record?
When was it created?
Who modified it?
When was it modified?
```

---

Example:

```sql
EMPLOYEE
```

| ID | NAME | CREATED_AT | UPDATED_AT |
|----|------|------------|------------|
| 1 | Sumit | 2026-06-27 10:00 | 2026-06-28 12:00 |

---

Without auditing:

```java
employee.setCreatedAt(
    LocalDateTime.now()
);
```

everywhere.

---

Tedious and error-prone.

---

Solution:

```java
@EnableJpaAuditing
```

---

# Why Do We Need It?

Without auditing:

```java
Employee employee =
    new Employee();

employee.setCreatedAt(
    LocalDateTime.now()
);

employee.setUpdatedAt(
    LocalDateTime.now()
);
```

---

Every save/update requires manual handling.

---

With auditing:

```java
employeeRepository.save(employee);
```

---

Spring automatically sets:

```text
createdAt
updatedAt
```

---

# Basic Setup

## Step 1

Enable auditing.

```java
@SpringBootApplication

@EnableJpaAuditing
public class Application {

    public static void main(
            String[] args) {

        SpringApplication.run(
            Application.class,
            args
        );
    }
}
```

---

# Step 2

Create Auditable Entity

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

    @LastModifiedDate
    private LocalDateTime updatedAt;
}
```

---

# Step 3

Save Entity

```java
employeeRepository.save(
    employee
);
```

---

Automatically:

```text
createdAt populated
updatedAt populated
```

---

# Common Auditing Annotations

## @CreatedDate

Stores:

```text
Creation Time
```

---

Example:

```java
@CreatedDate
private LocalDateTime createdAt;
```

---

Automatically set only once.

---

# @LastModifiedDate

Stores:

```text
Last Update Time
```

---

Example:

```java
@LastModifiedDate
private LocalDateTime updatedAt;
```

---

Updated every modification.

---

# @CreatedBy

Stores:

```text
Who Created Record
```

---

Example:

```java
@CreatedBy
private String createdBy;
```

---

# @LastModifiedBy

Stores:

```text
Who Updated Record
```

---

Example:

```java
@LastModifiedBy
private String modifiedBy;
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

Generated Table:

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

# What Happens Internally?

When:

```java
employeeRepository.save(employee);
```

---

Spring intercepts save operation.

---

Before insert:

```java
createdAt = now()
updatedAt = now()
```

---

Before update:

```java
updatedAt = now()
```

---

Automatically.

---

# EntityListeners Requirement

Very Important.

---

Need:

```java
@EntityListeners(
    AuditingEntityListener.class
)
```

---

Without it:

```java
@CreatedDate
```

and

```java
@LastModifiedDate
```

won't work.

---

# Using CreatedBy and ModifiedBy

Advanced Production Feature.

---

Entity:

```java
@CreatedBy
private String createdBy;

@LastModifiedBy
private String modifiedBy;
```

---

Need:

```java
AuditorAware<T>
```

implementation.

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

Configuration:

```java
@EnableJpaAuditing(
    auditorAwareRef =
        "auditorProvider"
)
```

---

Now:

```java
createdBy
modifiedBy
```

automatically populated.

---

# Real Production Example

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
    private String updatedBy;
}
```

---

Every entity inherits:

```text
createdAt
updatedAt
createdBy
updatedBy
```

---

# Database Example

Before Save:

```java
Employee employee =
    new Employee();

employee.setName("Sumit");
```

---

After Save:

| ID | NAME | CREATED_AT | UPDATED_AT |
|----|------|------------|------------|
| 1 | Sumit | 2026-06-27 10:00 | 2026-06-27 10:00 |

---

After Update:

```java
employee.setName(
    "Sumit Rajput"
);

save(employee);
```

---

Updated:

| ID | NAME | CREATED_AT | UPDATED_AT |
|----|------|------------|------------|
| 1 | Sumit Rajput | 2026-06-27 10:00 | 2026-06-28 15:00 |

---

Notice:

```text
createdAt unchanged
updatedAt changed
```

---

# Internal Spring Workflow

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

During Persist:

```text
EntityListener Invoked
         ↓
CreatedDate Populated
         ↓
Entity Saved
```

---

During Update:

```text
EntityListener Invoked
         ↓
LastModifiedDate Updated
         ↓
Entity Saved
```

---

# @EnableJpaAuditing vs Hibernate Timestamps

Interview Favorite.

---

## Hibernate

```java
@CreationTimestamp

@UpdateTimestamp
```

---

Hibernate-specific.

---

## Spring Data JPA

```java
@CreatedDate

@LastModifiedDate
```

---

Requires:

```java
@EnableJpaAuditing
```

---

Comparison:

| Feature | Hibernate | Spring Data |
|----------|------------|-------------|
| Creation Time | @CreationTimestamp | @CreatedDate |
| Update Time | @UpdateTimestamp | @LastModifiedDate |
| Created By | ❌ | ✅ |
| Modified By | ❌ | ✅ |
| Framework | Hibernate | Spring Data JPA |

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

Auditing won't work.

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

Expecting CreatedDate To Update

Wrong expectation:

```text
createdAt changes on update
```

---

Never happens.

---

Only:

```text
updatedAt changes
```

---

# Common Interview Questions

## What is @EnableJpaAuditing?

Enables Spring Data JPA auditing support.

---

## What does it provide?

Automatic:

```text
Created Date
Modified Date
Created By
Modified By
```

---

## Is it enough alone?

No.

Need:

```java
@EntityListeners(
 AuditingEntityListener.class
)
```

---

## Which annotations require it?

```java
@CreatedDate
@LastModifiedDate
@CreatedBy
@LastModifiedBy
```

---

## How to populate CreatedBy?

Implement:

```java
AuditorAware<T>
```

---

# Enterprise Best Practice

Create:

```java
BaseEntity
```

---

Containing:

```java
@CreatedDate

@LastModifiedDate

@CreatedBy

@LastModifiedBy
```

---

Enable:

```java
@EnableJpaAuditing
```

---

All entities inherit auditing automatically.

---

# Key Points To Remember

- `@EnableJpaAuditing` enables auditing support in Spring Data JPA.
- Works with `@CreatedDate`, `@LastModifiedDate`, `@CreatedBy`, and `@LastModifiedBy`.
- Must be placed on a configuration class.
- Requires `AuditingEntityListener`.
- Eliminates manual timestamp management.
- Supports user tracking through `AuditorAware`.
- Commonly used with `@MappedSuperclass`.
- Widely used in enterprise applications.
- Important Spring Data JPA interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EnableJpaAuditing` | Enable Auditing |
| `@CreatedDate` | Creation Timestamp |
| `@LastModifiedDate` | Update Timestamp |
| `@CreatedBy` | Creator |
| `@LastModifiedBy` | Last Modifier |
| `AuditorAware` | Current User Provider |

---

# Interview Shortcut

```java
@EnableJpaAuditing
```

Meaning:

```text
Spring Data Auditing Enabled
           ↓
@CreatedDate Works
@LastModifiedDate Works
@CreatedBy Works
@LastModifiedBy Works
           ↓
Automatic Audit Information
For Every Entity
```

Typical enterprise setup:

```java
@SpringBootApplication

@EnableJpaAuditing
public class Application {
}
```

This is the standard Spring Boot approach for automatically tracking entity creation and modification information.