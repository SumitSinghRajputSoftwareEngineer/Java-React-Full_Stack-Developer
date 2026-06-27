
## Definition

`@Id` is a JPA annotation used to mark a field as the **Primary Key** of an entity.

It tells JPA/Hibernate:

> This field uniquely identifies each row in the database table.

Package:

```java
import jakarta.persistence.Id;
```

Prior to Spring Boot 3:

```java
import javax.persistence.Id;
```

---

# Why Do We Need @Id?

Every database table should have a unique identifier.

Example:

```sql
EMPLOYEE
```

| ID | NAME |
|----|------|
| 1 | Sumit |
| 2 | John |
| 3 | David |

---

Without Primary Key:

```text
How will Hibernate identify a row?
How will update work?
How will delete work?
How will findById() work?
```

Impossible.

---

Therefore every entity must have:

```java
@Id
```

---

# Basic Syntax

```java
@Entity
public class Employee {

    @Id
    private Long id;
}
```

---

Meaning:

```text
id = Primary Key
```

---

# Simplest Example

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String name;
}
```

---

Database:

```sql
EMPLOYEE
```

| ID | NAME |
|----|------|
| 1 | Sumit |
| 2 | Raj |
| 3 | Amit |

---

Hibernate uses:

```text
ID
```

to identify records.

---

# Is @Id Mandatory?

Yes.

---

Wrong:

```java
@Entity
public class Employee {

    private Long id;
}
```

---

Application Startup:

```text
Failure
```

Exception:

```text
No identifier specified for entity
```

---

Correct:

```java
@Entity
public class Employee {

    @Id
    private Long id;
}
```

---

# Primary Key Characteristics

A primary key should be:

```text
Unique
Not Null
Immutable (preferably)
```

---

Example:

```java
@Id
private Long id;
```

---

Valid:

```text
1
2
3
4
```

---

Invalid:

```text
Duplicate Values
Null Values
```

---

# Manual ID Example

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String name;
}
```

---

Save:

```java
Employee e = new Employee();

e.setId(100L);

employeeRepository.save(e);
```

---

SQL:

```sql
INSERT INTO employee
(id,name)
VALUES
(100,'Sumit')
```

---

# Auto Generated ID

Most common approach.

---

```java
@Entity
public class Employee {

    @Id

    @GeneratedValue
    private Long id;

    private String name;
}
```

---

Save:

```java
Employee e =
    new Employee();

e.setName("Sumit");

employeeRepository.save(e);
```

---

Hibernate:

```sql
INSERT INTO employee(name)
VALUES ('Sumit')
```

---

Database Generates:

```text
ID = 1
```

---

# @Id + @GeneratedValue

Very common combination.

---

```java
@Id

@GeneratedValue(
    strategy =
    GenerationType.IDENTITY
)
private Long id;
```

---

Meaning:

```text
Database Generates Primary Key
```

---

# Supported ID Types

Common:

```java
Long
Integer
String
UUID
```

---

Examples:

```java
@Id
private Long id;
```

---

```java
@Id
private Integer id;
```

---

```java
@Id
private String employeeCode;
```

---

```java
@Id
private UUID id;
```

---

# UUID Example

```java
@Entity
public class Employee {

    @Id

    @GeneratedValue
    private UUID id;
}
```

---

Generated Value:

```text
550e8400-e29b-41d4-a716-446655440000
```

---

Common in:

```text
Microservices
Distributed Systems
```

---

# Composite Primary Key

Sometimes one field is not enough.

---

Example:

```text
studentId
courseId
```

Together form PK.

---

Use:

```java
@EmbeddedId
```

or

```java
@IdClass
```

---

Example:

```java
@Entity
@IdClass(StudentCourseId.class)
public class Enrollment {
}
```

---

# How Hibernate Uses @Id

Suppose:

```java
Employee emp =
    employeeRepository.findById(1L);
```

---

Hibernate Executes:

```sql
SELECT *
FROM employee
WHERE id = 1
```

---

ID is the basis of:

```text
Find
Update
Delete
Caching
Relationships
```

---

# Entity Identity

Interview Favorite.

---

Two objects:

```java
Employee e1 =
    employeeRepository.findById(1L);

Employee e2 =
    employeeRepository.findById(1L);
```

---

Hibernate knows:

```text
Both represent same DB row
```

Because of:

```java
@Id
```

---

# First Level Cache Usage

Hibernate Cache Key:

```text
Entity Type
+
@Id Value
```

---

Example:

```text
Employee
+
1
```

---

Cache Key:

```text
Employee#1
```

---

Without @Id:

```text
Caching Impossible
```

---

# Relationships Depend On @Id

Example:

```java
@Entity
public class Employee {

    @Id
    private Long id;
}
```

---

```java
@Entity
public class Address {

    @ManyToOne
    private Employee employee;
}
```

---

Database:

```sql
ADDRESS
(
 ID,
 EMPLOYEE_ID
)
```

---

Foreign Key references:

```java
Employee.id
```

---

# @Id vs @Column

Interview Question.

---

## @Id

Defines:

```text
Primary Key
```

---

Example:

```java
@Id
private Long id;
```

---

## @Column

Defines:

```text
Column Mapping
```

---

Example:

```java
@Column(name="emp_name")
private String name;
```

---

Comparison:

| Feature | @Id | @Column |
|----------|------|---------|
| Primary Key | ✅ | ❌ |
| Column Mapping | ❌ | ✅ |
| Mandatory | ✅ | ❌ |

---

# @Id vs @GeneratedValue

Interview Question.

---

## @Id

Marks PK.

---

```java
@Id
private Long id;
```

---

## @GeneratedValue

Generates PK.

---

```java
@GeneratedValue
```

---

Usually used together:

```java
@Id
@GeneratedValue
private Long id;
```

---

# Internal Hibernate Processing

Startup:

```java
@Id
private Long id;
```

---

Hibernate Metadata:

```text
Primary Key Field
Column Name
Data Type
Generation Strategy
```

---

Stored in:

```java
SessionFactory
```

---

Used for:

```text
CRUD Operations
Caching
Dirty Checking
Relationships
```

---

# Common Mistakes

## Mistake 1

No @Id

Wrong:

```java
@Entity
public class Employee {

    private Long id;
}
```

---

Startup failure.

---

## Mistake 2

Changing Primary Key

Wrong:

```java
employee.setId(100L);
```

after persistence.

---

Primary keys should be stable.

---

## Mistake 3

Using Non-Unique Values

Wrong:

```java
@Id
private String department;
```

---

Department:

```text
HR
IT
HR
IT
```

Not unique.

---

Bad primary key.

---

# Common Interview Questions

## What is @Id?

Marks a field as the primary key.

---

## Is @Id mandatory?

Yes.

Every JPA entity requires it.

---

## Can Entity Have Multiple @Id Fields?

Not directly.

Use:

```java
@IdClass
```

or

```java
@EmbeddedId
```

---

## Does @Id Automatically Generate Values?

No.

Use:

```java
@GeneratedValue
```

---

## Why Is @Id Important?

Hibernate uses it for:

```text
Find
Update
Delete
Cache
Relationships
Identity
```

---

## Can String Be Used As @Id?

Yes.

---

Example:

```java
@Id
private String employeeCode;
```

---

# Real-World Production Example

```java
@Entity

@Table(name = "employees")
public class Employee {

    @Id

    @GeneratedValue(
        strategy =
        GenerationType.IDENTITY
    )
    private Long id;

    private String name;

    private String email;
}
```

---

Save:

```java
employeeRepository.save(
    new Employee()
);
```

---

Hibernate:

```sql
INSERT INTO employees
```

---

Database:

```text
Generates ID Automatically
```

---

# Key Points To Remember

- `@Id` marks the primary key field.
- Every JPA entity must have an `@Id`.
- Primary key uniquely identifies a row.
- Hibernate relies on it for CRUD operations.
- Works with Long, Integer, String, UUID, etc.
- Often combined with `@GeneratedValue`.
- Required for relationships and caching.
- Used internally as the entity identity.
- Composite keys use `@IdClass` or `@EmbeddedId`.
- One of the most important JPA interview topics.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Id` | Primary Key |
| `@GeneratedValue` | Auto Generate PK |
| `@Column` | Column Mapping |
| `@EmbeddedId` | Composite Key |
| `@IdClass` | Composite Key |
| `@Entity` | JPA Entity |

---

# Interview Shortcut

```java
@Entity
public class Employee {

    @Id

    @GeneratedValue(
        strategy =
        GenerationType.IDENTITY
    )
    private Long id;
}
```

Meaning:

```text
Employee Table
      ↓
Primary Key = id
      ↓
Database Generates Value
      ↓
Hibernate Uses It For Identity
```

This is one of the most fundamental annotations in JPA and Spring Data JPA.