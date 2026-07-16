
## Definition

`@EntityListeners` is a JPA annotation used to register one or more **listener classes** that receive lifecycle callbacks whenever an entity is created, updated, loaded, or deleted.

It tells JPA/Hibernate:

> Execute specific logic automatically when entity lifecycle events occur.

Package:

```java
import jakarta.persistence.EntityListeners;
```

Prior to Spring Boot 3:

```java
import javax.persistence.EntityListeners;
```

---

# Why Do We Need @EntityListeners?

Suppose you want to:

```text
Set audit timestamps
Log changes
Validate data
Track user activity
Send notifications
```

whenever an entity is saved or updated.

---

Without listeners:

```java
employee.setCreatedAt(
    LocalDateTime.now()
);
```

must be written everywhere.

---

Problem:

```text
Duplicate Code
Maintenance Issues
Human Errors
```

---

Solution:

```java
@EntityListeners
```

---

# Basic Idea

Entity lifecycle:

```text
Create Entity
Update Entity
Delete Entity
Load Entity
```

---

At each stage:

```text
Run Custom Logic
```

automatically.

---

# Simple Example

## Entity

```java
@Entity

@EntityListeners(
    EmployeeListener.class
)
public class Employee {

    @Id

    @GeneratedValue
    private Long id;

    private String name;
}
```

---

## Listener

```java
public class EmployeeListener {

    @PrePersist
    public void beforeInsert(
            Employee employee) {

        System.out.println(
            "Before Insert"
        );
    }
}
```

---

When:

```java
employeeRepository.save(
    employee
);
```

---

Output:

```text
Before Insert
```

---

Automatically executed.

---

# Lifecycle Callback Methods

Very Important Interview Topic.

---

## @PrePersist

Before insert.

```java
@PrePersist
public void beforeInsert() {
}
```

---

Executed before:

```sql
INSERT
```

---

## @PostPersist

After insert.

```java
@PostPersist
public void afterInsert() {
}
```

---

Executed after:

```sql
INSERT
```

---

## @PreUpdate

Before update.

```java
@PreUpdate
public void beforeUpdate() {
}
```

---

Executed before:

```sql
UPDATE
```

---

## @PostUpdate

After update.

```java
@PostUpdate
public void afterUpdate() {
}
```

---

Executed after:

```sql
UPDATE
```

---

## @PreRemove

Before delete.

```java
@PreRemove
public void beforeDelete() {
}
```

---

Executed before:

```sql
DELETE
```

---

## @PostRemove

After delete.

```java
@PostRemove
public void afterDelete() {
}
```

---

Executed after:

```sql
DELETE
```

---

## @PostLoad

After entity loaded.

```java
@PostLoad
public void afterLoad() {
}
```

---

Executed after:

```sql
SELECT
```

---

# Complete Lifecycle Example

```java
public class EmployeeListener {

    @PrePersist
    public void prePersist(
            Employee e) {

        System.out.println(
            "Before Insert"
        );
    }

    @PostPersist
    public void postPersist(
            Employee e) {

        System.out.println(
            "After Insert"
        );
    }

    @PreUpdate
    public void preUpdate(
            Employee e) {

        System.out.println(
            "Before Update"
        );
    }

    @PostUpdate
    public void postUpdate(
            Employee e) {

        System.out.println(
            "After Update"
        );
    }
}
```

---

# Real Production Example

## Audit Fields

```java
public class AuditListener {

    @PrePersist
    public void prePersist(
            BaseEntity entity) {

        entity.setCreatedAt(
            LocalDateTime.now()
        );
    }

    @PreUpdate
    public void preUpdate(
            BaseEntity entity) {

        entity.setUpdatedAt(
            LocalDateTime.now()
        );
    }
}
```

---

Automatically updates timestamps.

---

# Spring Data Auditing Example

Most common use case.

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

Without:

```java
@EntityListeners(
    AuditingEntityListener.class
)
```

---

These annotations won't work:

```java
@CreatedDate
@LastModifiedDate
@CreatedBy
@LastModifiedBy
```

---

# Multiple Listeners

Possible.

---

```java
@EntityListeners({
    AuditListener.class,
    LoggingListener.class
})
```

---

Both execute.

---

# Listener With Entity Methods

Alternative approach.

---

Instead of external class.

---

```java
@Entity
public class Employee {

    @PrePersist
    public void beforeInsert() {

        System.out.println(
            "Saving Employee"
        );
    }
}
```

---

Works.

---

But for reusable logic:

```java
@EntityListeners
```

is preferred.

---

# Internal Workflow

Save Entity:

```java
repository.save(entity);
```

---

Hibernate:

```text
Detect @EntityListeners
            ↓
Find Matching Callback
            ↓
Execute Callback Method
            ↓
Run SQL
```

---

Automatically.

---

# Real Enterprise Audit Example

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

    @CreatedBy
    private String createdBy;

    @LastModifiedBy
    private String modifiedBy;
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

Whenever:

```java
save()
```

or

```java
update()
```

---

Audit fields are automatically populated.

---

# @EntityListeners vs Entity Callback Methods

Interview Favorite.

---

## Callback Methods Inside Entity

```java
@Entity
public class Employee {

    @PrePersist
    void save() {
    }
}
```

---

Logic tied to entity.

---

## EntityListeners

```java
@EntityListeners(
    AuditListener.class
)
```

---

Reusable.

---

Cleaner architecture.

---

Comparison:

| Feature | Callback Method | EntityListener |
|----------|---------------|---------------|
| Reusable | ❌ | ✅ |
| Centralized | ❌ | ✅ |
| Cleaner | ❌ | ✅ |
| Enterprise Friendly | Moderate | Excellent |

---

# Common Mistakes

## Mistake 1

Forgetting EntityListeners

Wrong:

```java
@CreatedDate
private LocalDateTime createdAt;
```

---

Without:

```java
@EntityListeners(
 AuditingEntityListener.class
)
```

---

Result:

```java
createdAt = null
```

---

# Mistake 2

Using Non-Public Methods

Bad practice:

```java
private void prePersist() {
}
```

---

Prefer:

```java
public void prePersist() {
}
```

---

# Mistake 3

Heavy Business Logic

Wrong:

```java
@PrePersist
```

calling:

```text
External APIs
Long Operations
```

---

Keep callbacks lightweight.

---

# Common Interview Questions

## What is @EntityListeners?

Registers listener classes for entity lifecycle events.

---

## Why is it used?

To execute logic automatically during entity lifecycle.

---

## Which events are supported?

```java
@PrePersist
@PostPersist

@PreUpdate
@PostUpdate

@PreRemove
@PostRemove

@PostLoad
```

---

## Why is it required for auditing?

Because:

```java
AuditingEntityListener
```

populates audit fields.

---

## Can multiple listeners be used?

Yes.

---

## Difference Between @EntityListeners and @PrePersist?

`@PrePersist` is a callback event.

`@EntityListeners` registers the class containing callback methods.

---

# Enterprise Best Practice

Create:

```java
BaseEntity
```

---

Use:

```java
@EntityListeners(
    AuditingEntityListener.class
)
```

---

For:

```java
@CreatedDate
@LastModifiedDate
@CreatedBy
@LastModifiedBy
```

---

All entities inherit auditing support.

---

# Key Points To Remember

- `@EntityListeners` registers lifecycle listener classes.
- Used for auditing, logging, validation, and tracking.
- Works with JPA lifecycle callbacks.
- Supports PrePersist, PostPersist, PreUpdate, PostUpdate, PreRemove, PostRemove, and PostLoad.
- Required by Spring Data JPA auditing.
- Can register multiple listeners.
- Promotes reusable and centralized logic.
- Widely used in enterprise applications.
- Very important JPA interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EntityListeners` | Register Listener |
| `@PrePersist` | Before Insert |
| `@PostPersist` | After Insert |
| `@PreUpdate` | Before Update |
| `@PostUpdate` | After Update |
| `@PreRemove` | Before Delete |
| `@PostRemove` | After Delete |
| `@PostLoad` | After Fetch |

---

# Interview Shortcut

```java
@EntityListeners(
    AuditingEntityListener.class
)
```

Meaning:

```text
Entity Saved
       ↓
Listener Invoked
       ↓
Audit Fields Populated
       ↓
SQL Executed
```

Typical Spring Boot auditing setup:

```java
@MappedSuperclass

@EntityListeners(
    AuditingEntityListener.class
)
public abstract class BaseEntity {
}
```

This is the standard JPA mechanism for executing automatic logic during entity lifecycle events such as insert, update, delete, and load operations.