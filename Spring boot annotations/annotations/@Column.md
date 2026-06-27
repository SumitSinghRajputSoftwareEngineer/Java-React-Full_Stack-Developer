
## Definition

`@Column` is a JPA annotation used to customize the mapping between an entity field and a database column.

It tells JPA/Hibernate:

> This field should be mapped to a database column with the specified configuration.

Package:

```java
import jakarta.persistence.Column;
```

Prior to Spring Boot 3:

```java
import javax.persistence.Column;
```

---

# Why Do We Need @Column?

Without `@Column`, Hibernate automatically maps fields to columns.

Example:

```java
@Entity
public class Employee {

    private String name;
}
```

Hibernate creates:

```sql
NAME
```

column automatically.

---

Sometimes we need:

```text
Custom Column Name
Length Restriction
Unique Constraint
Nullable Control
Precision/Scale
Insert/Update Control
```

---

Solution:

```java
@Column
```

---

# Basic Syntax

```java
@Column
private String name;
```

---

Usually:

```java
@Column(name = "employee_name")
private String name;
```

---

Meaning:

```text
Java Field: name
        ↓
DB Column: employee_name
```

---

# Example Without @Column

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String name;
}
```

---

Hibernate:

```sql
CREATE TABLE employee
(
    id BIGINT,
    name VARCHAR(255)
);
```

---

# Example With @Column

```java
@Entity
public class Employee {

    @Id
    private Long id;

    @Column(name = "employee_name")
    private String name;
}
```

---

Hibernate:

```sql
CREATE TABLE employee
(
    id BIGINT,
    employee_name VARCHAR(255)
);
```

---

# Most Common Attributes

```java
name
nullable
unique
length
precision
scale
insertable
updatable
columnDefinition
```

---

# 1. name Attribute

Most commonly used.

---

```java
@Column(name = "employee_name")
private String name;
```

---

Java:

```java
employee.getName();
```

---

Database:

```sql
employee_name
```

---

# 2. nullable Attribute

Controls NULL values.

---

```java
@Column(nullable = false)
private String email;
```

---

Generated SQL:

```sql
email VARCHAR(255) NOT NULL
```

---

Valid:

```text
abc@gmail.com
```

---

Invalid:

```text
NULL
```

---

# 3. unique Attribute

Creates unique constraint.

---

```java
@Column(unique = true)
private String email;
```

---

Generated SQL:

```sql
UNIQUE(email)
```

---

Valid:

```text
sumit@gmail.com
john@gmail.com
```

---

Invalid:

```text
sumit@gmail.com
sumit@gmail.com
```

---

# 4. length Attribute

Controls VARCHAR length.

---

```java
@Column(length = 100)
private String name;
```

---

Generated SQL:

```sql
VARCHAR(100)
```

---

Default:

```java
255
```

---

Example:

```java
@Column(length = 50)
private String firstName;
```

---

SQL:

```sql
VARCHAR(50)
```

---

# 5. precision Attribute

Used for decimal values.

---

Example:

```java
@Column(
    precision = 10,
    scale = 2
)
private BigDecimal salary;
```

---

Generated SQL:

```sql
DECIMAL(10,2)
```

---

Meaning:

```text
Total Digits = 10
Digits After Decimal = 2
```

---

Valid:

```text
12345678.99
```

---

Invalid:

```text
12345678901.99
```

---

# 6. scale Attribute

Works with precision.

---

```java
@Column(
    precision = 12,
    scale = 3
)
private BigDecimal amount;
```

---

Generated:

```sql
DECIMAL(12,3)
```

---

Example:

```text
12345.123
```

---

# 7. insertable Attribute

Controls INSERT participation.

---

```java
@Column(
    insertable = false
)
private LocalDateTime createdDate;
```

---

Hibernate INSERT:

```sql
INSERT INTO employee(name)
```

---

createdDate excluded.

---

Useful when DB generates values.

---

Example:

```sql
DEFAULT CURRENT_TIMESTAMP
```

---

# 8. updatable Attribute

Controls UPDATE participation.

---

```java
@Column(
    updatable = false
)
private LocalDateTime createdDate;
```

---

INSERT:

```sql
createdDate included
```

---

UPDATE:

```sql
createdDate excluded
```

---

Useful for:

```text
Audit Fields
Created Date
Created By
```

---

# Example

```java
@Column(
    updatable = false
)
private LocalDateTime createdDate;
```

---

# 9. columnDefinition

Allows custom SQL definition.

---

```java
@Column(
    columnDefinition =
    "TEXT"
)
private String description;
```

---

Generated SQL:

```sql
description TEXT
```

---

Another Example:

```java
@Column(
    columnDefinition =
    "TIMESTAMP DEFAULT CURRENT_TIMESTAMP"
)
private LocalDateTime createdDate;
```

---

# Complete Example

```java
@Entity
public class Employee {

    @Id
    private Long id;

    @Column(
        name = "employee_name",
        nullable = false,
        length = 100
    )
    private String name;

    @Column(
        unique = true
    )
    private String email;

    @Column(
        precision = 10,
        scale = 2
    )
    private BigDecimal salary;
}
```

---

Generated SQL

```sql
CREATE TABLE employee
(
 id BIGINT,

 employee_name
 VARCHAR(100) NOT NULL,

 email
 VARCHAR(255) UNIQUE,

 salary
 DECIMAL(10,2)
);
```

---

# @Column vs @Table

Interview Question.

---

## @Table

Controls:

```text
Table-Level Mapping
```

---

Example:

```java
@Table(name="employees")
```

---

## @Column

Controls:

```text
Column-Level Mapping
```

---

Example:

```java
@Column(name="emp_name")
```

---

Comparison:

| Annotation | Controls |
|------------|-----------|
| @Table | Table |
| @Column | Column |

---

# @Column vs Validation Annotations

Interview Question.

---

## @Column(nullable = false)

Database Constraint.

---

```java
@Column(nullable=false)
```

---

Executed:

```text
At Database Level
```

---

## @NotNull

Validation Constraint.

---

```java
@NotNull
```

---

Executed:

```text
Before Database Call
```

---

Best Practice:

```java
@NotNull

@Column(nullable=false)
```

Use both.

---

# Internal Hibernate Processing

Example:

```java
@Column(
    name = "employee_name",
    nullable = false
)
private String name;
```

---

Hibernate Metadata:

```text
Column Name
Type
Length
Nullability
Uniqueness
Precision
Scale
```

---

Stored inside:

```java
SessionFactory
```

---

Used for:

```sql
DDL Generation
INSERT
UPDATE
SELECT
```

---

# Common Mistakes

## Mistake 1

Using @Column Instead Of Validation

Wrong:

```java
@Column(nullable=false)
```

Expecting API validation.

---

Use:

```java
@NotNull
```

also.

---

# Mistake 2

Large Text Without columnDefinition

Wrong:

```java
@Column(length=10000)
```

---

Better:

```java
@Column(
    columnDefinition="TEXT"
)
```

---

# Mistake 3

Confusing Length With Validation

Wrong:

```java
@Column(length=10)
```

---

Does NOT validate request length.

---

Use:

```java
@Size(max=10)
```

---

# Common Interview Questions

## What is @Column?

Used to customize column mapping.

---

## Is @Column mandatory?

No.

---

## What is default String length?

```java
255
```

---

## Difference Between @NotNull and nullable=false?

`@NotNull`

```text
Application Validation
```

---

`nullable=false`

```text
Database Constraint
```

---

## What is precision?

Total digits.

---

## What is scale?

Digits after decimal.

---

## Can @Column create unique constraints?

Yes.

```java
unique = true
```

---

# Real-World Production Example

```java
@Entity
@Table(name = "employees")
public class Employee {

    @Id
    @GeneratedValue
    private Long id;

    @Column(
        name = "employee_name",
        nullable = false,
        length = 100
    )
    private String name;

    @Column(
        unique = true,
        nullable = false
    )
    private String email;

    @Column(
        precision = 12,
        scale = 2
    )
    private BigDecimal salary;

    @Column(
        updatable = false
    )
    private LocalDateTime createdDate;
}
```

---

# Key Points To Remember

- `@Column` customizes column mapping.
- Optional annotation.
- Used for column name customization.
- Supports nullability control.
- Supports unique constraints.
- Supports VARCHAR length configuration.
- Supports precision and scale for decimals.
- Supports insert/update control.
- Supports custom SQL definitions.
- Works at the column level.
- One of the most frequently used JPA annotations.

---

# Quick Comparison

| Attribute | Purpose |
|------------|----------|
| `name` | Custom Column Name |
| `nullable` | NOT NULL Constraint |
| `unique` | Unique Constraint |
| `length` | VARCHAR Length |
| `precision` | Total Decimal Digits |
| `scale` | Decimal Places |
| `insertable` | Include In INSERT |
| `updatable` | Include In UPDATE |
| `columnDefinition` | Custom SQL |

---

# Interview Shortcut

```java
@Column(
    name = "employee_name",
    nullable = false,
    unique = true,
    length = 100
)
private String name;
```

Meaning:

```text
Java Field = name
       ↓
DB Column = employee_name
       ↓
NOT NULL
       ↓
UNIQUE
       ↓
VARCHAR(100)
```

This is one of the most commonly used annotations in Spring Data JPA and Hibernate.