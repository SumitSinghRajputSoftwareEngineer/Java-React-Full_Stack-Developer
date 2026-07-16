
## Definition

`@MappedSuperclass` is a JPA annotation used to define a **parent class whose fields should be inherited by entity classes**, but which itself is **not an entity and does not have its own table**.

It tells Hibernate/JPA:

> Copy the fields from this parent class into child entity tables, but do not create a table for the parent.

Package:

```java
import jakarta.persistence.MappedSuperclass;
```

Prior to Spring Boot 3:

```java
import javax.persistence.MappedSuperclass;
```

---

# Why Do We Need @MappedSuperclass?

Consider multiple entities:

```java
Employee
Customer
Product
Order
```

---

All contain:

```java
id
createdDate
updatedDate
createdBy
```

---

Without inheritance:

```java
@Entity
class Employee {

    private Long id;
    private LocalDateTime createdDate;
    private LocalDateTime updatedDate;
}
```

---

```java
@Entity
class Customer {

    private Long id;
    private LocalDateTime createdDate;
    private LocalDateTime updatedDate;
}
```

---

Code duplication everywhere.

---

Solution:

```java
@MappedSuperclass
```

---

# Basic Idea

Create a parent class:

```java
BaseEntity
```

---

Put common fields there.

---

Child entities inherit them.

---

Hibernate copies fields into child tables.

---

No table is created for:

```java
BaseEntity
```

---

# Basic Syntax

```java
@MappedSuperclass
public class BaseEntity {

}
```

---

# Simple Example

## Parent

```java
@MappedSuperclass
public class BaseEntity {

    @Id

    @GeneratedValue
    private Long id;

    private LocalDateTime createdDate;

    private LocalDateTime updatedDate;
}
```

---

## Employee

```java
@Entity
public class Employee
       extends BaseEntity {

    private String name;
}
```

---

## Customer

```java
@Entity
public class Customer
       extends BaseEntity {

    private String email;
}
```

---

# Generated Tables

### EMPLOYEE

```sql
ID
CREATED_DATE
UPDATED_DATE
NAME
```

---

### CUSTOMER

```sql
ID
CREATED_DATE
UPDATED_DATE
EMAIL
```

---

Notice:

```text
No BASE_ENTITY Table
```

---

Only fields are inherited.

---

# What Hibernate Actually Does

Parent:

```java
@MappedSuperclass
class BaseEntity
```

---

Child:

```java
@Entity
class Employee
       extends BaseEntity
```

---

Hibernate behaves as if:

```java
@Entity
class Employee {

    private Long id;

    private LocalDateTime createdDate;

    private LocalDateTime updatedDate;

    private String name;
}
```

---

Fields are copied.

---

# No Separate Table

Important Interview Question.

---

For:

```java
@MappedSuperclass
```

Hibernate does NOT create:

```sql
BASE_ENTITY
```

table.

---

Generated:

```sql
EMPLOYEE
CUSTOMER
```

only.

---

# Common Real-World Example

## Audit Fields

Very common in enterprise projects.

---

```java
@MappedSuperclass
public abstract class Auditable {

    @Id

    @GeneratedValue
    private Long id;

    private LocalDateTime createdAt;

    private LocalDateTime updatedAt;

    private String createdBy;

    private String updatedBy;
}
```

---

Used by:

```java
User
Product
Order
Invoice
```

---

Avoids duplication.

---

# Using Annotations Inside @MappedSuperclass

All JPA annotations work.

---

Example:

```java
@MappedSuperclass
public abstract class BaseEntity {

    @Id

    @GeneratedValue
    private Long id;

    @Version
    private Long version;
}
```

---

Children inherit:

```java
@Id

@GeneratedValue

@Version
```

automatically.

---

# Abstract Class Example

Recommended approach.

---

```java
@MappedSuperclass
public abstract class BaseEntity {

}
```

---

Why?

Because:

```text
BaseEntity
```

should never be instantiated.

---

Only child entities should exist.

---

# @MappedSuperclass vs @Inheritance

Most Asked Interview Question.

---

## @MappedSuperclass

```text
Shares Fields Only
```

---

No table.

---

No polymorphic queries.

---

Example:

```java
BaseEntity
      ↑
Employee
Customer
```

---

Generated:

```sql
EMPLOYEE
CUSTOMER
```

---

No:

```sql
BASE_ENTITY
```

---

## @Inheritance

```text
Creates Entity Hierarchy
```

---

Parent is an entity.

---

Has table mapping.

---

Supports polymorphism.

---

Example:

```java
Employee
      ↑
FullTimeEmployee
ContractEmployee
```

---

Generated:

```sql
EMPLOYEE
FULL_TIME_EMPLOYEE
CONTRACT_EMPLOYEE
```

---

Comparison:

| Feature | @MappedSuperclass | @Inheritance |
|----------|------------------|-------------|
| Parent Table | ❌ | ✅ |
| Child Tables | ✅ | ✅ |
| Polymorphic Queries | ❌ | ✅ |
| Field Reuse | ✅ | ✅ |
| Entity Hierarchy | ❌ | ✅ |

---

# @MappedSuperclass vs @Entity

Interview Favorite.

---

## @Entity

```java
@Entity
class Employee
```

---

Creates table.

---

Can be queried.

---

Can be persisted.

---

## @MappedSuperclass

```java
@MappedSuperclass
class BaseEntity
```

---

No table.

---

Cannot be queried.

---

Cannot be persisted directly.

---

# Can Repository Be Created?

Wrong:

```java
public interface BaseEntityRepository
    extends JpaRepository<
        BaseEntity,
        Long
    >
{
}
```

---

Error.

---

Reason:

```java
BaseEntity
```

is NOT an entity.

---

Cannot be managed directly.

---

# Can We Query It?

Wrong:

```java
SELECT b
FROM BaseEntity b
```

---

Error.

---

Reason:

```text
No Entity
No Table
```

---

# Internal Hibernate Processing

During startup:

```text
Detect @MappedSuperclass
        ↓
Extract Fields
        ↓
Merge Into Child Entities
```

---

Metadata becomes:

```text
Employee
    ↓
Inherited Fields
    +
Own Fields
```

---

Then table generated.

---

# Production Example

```java
@MappedSuperclass
public abstract class BaseEntity {

    @Id

    @GeneratedValue(
        strategy =
            GenerationType.IDENTITY
    )
    private Long id;

    @CreationTimestamp
    private LocalDateTime createdAt;

    @UpdateTimestamp
    private LocalDateTime updatedAt;

    @Version
    private Long version;
}
```

---

Employee:

```java
@Entity
public class Employee
       extends BaseEntity {

    private String name;

    private String department;
}
```

---

Generated Table:

```sql
EMPLOYEE
(
 ID,
 CREATED_AT,
 UPDATED_AT,
 VERSION,

 NAME,
 DEPARTMENT
)
```

---

# Common Mistakes

## Mistake 1

Expecting Parent Table

Wrong expectation:

```sql
BASE_ENTITY
```

---

Never created.

---

# Mistake 2

Creating Repository For Parent

Wrong:

```java
JpaRepository<BaseEntity, Long>
```

---

Not allowed.

---

# Mistake 3

Trying JPQL Query

Wrong:

```java
SELECT b FROM BaseEntity b
```

---

Fails.

---

# Common Interview Questions

## What is @MappedSuperclass?

A non-entity parent class whose fields are inherited by entities.

---

## Does it create a table?

No.

---

## Can it have @Id?

Yes.

Children inherit it.

---

## Can it have @Version?

Yes.

---

## Can it be queried?

No.

---

## Can repository be created?

No.

---

## Difference Between @MappedSuperclass and @Inheritance?

Field reuse vs entity hierarchy.

---

## When should it be used?

For common fields:

```text
ID
Audit Columns
Version
Created Date
Updated Date
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
@Id
@GeneratedValue
@Version
createdAt
updatedAt
```

---

Let every entity inherit it.

---

Example:

```java
User
Order
Invoice
Product
Customer
```

---

Reduces duplication dramatically.

---

# Key Points To Remember

- `@MappedSuperclass` shares fields among entities.
- Parent class is NOT an entity.
- No table is created for the parent.
- Child entities inherit all mappings.
- Supports `@Id`, `@Version`, `@Column`, etc.
- Cannot be queried directly.
- Cannot have a repository.
- Commonly used for audit fields and IDs.
- Different from `@Inheritance`.
- Extremely common in enterprise Spring Boot projects.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Entity` | Persistent Entity |
| `@MappedSuperclass` | Share Fields |
| `@Inheritance` | Entity Hierarchy |
| `@Embeddable` | Value Object |
| `@Embedded` | Embed Value Object |

---

# Interview Shortcut

```java
@MappedSuperclass
public abstract class BaseEntity {

    @Id
    private Long id;

    private LocalDateTime createdAt;
}
```

Meaning:

```text
BaseEntity
      ↓
No Table Created
      ↓
Fields Copied To Children
      ↓
Employee Table Gets:
ID
CREATED_AT
NAME
```

This is the standard enterprise approach for sharing common fields such as IDs, audit columns, and version fields across multiple JPA entities.