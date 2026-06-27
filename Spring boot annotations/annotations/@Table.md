
## Definition

`@Table` is a JPA annotation used to customize the database table mapping for an entity.

It tells JPA/Hibernate:

> This entity should be mapped to the specified database table.

Package:

```java
import jakarta.persistence.Table;
```

Prior to Spring Boot 3:

```java
import javax.persistence.Table;
```

---

# Why Do We Need @Table?

Without `@Table`, Hibernate automatically derives the table name from the entity class name.

Example:

```java
@Entity
public class Employee {
}
```

Hibernate may create:

```sql
employee
```

or

```sql
EMPLOYEE
```

depending on naming strategy.

---

Sometimes database tables already exist:

```sql
tbl_employee

employee_master

employees

emp_details
```

To map an entity to those tables:

```java
@Table(name = "employees")
```

---

# Basic Syntax

```java
@Entity

@Table(name = "employees")
public class Employee {
}
```

---

Meaning:

```text
Employee Entity
       ↓
employees Table
```

---

# Example Without @Table

```java
@Entity
public class Student {

    @Id
    private Long id;
}
```

---

Hibernate creates:

```sql
student
```

---

# Example With @Table

```java
@Entity

@Table(name = "students")
public class Student {

    @Id
    private Long id;
}
```

---

Hibernate creates:

```sql
students
```

---

# Internal Mapping

```java
@Entity

@Table(name = "employees")
public class Employee {
}
```

---

Hibernate Metadata:

```text
Class  → Employee

Table  → employees
```

---

# Is @Table Mandatory?

No.

---

Valid:

```java
@Entity
public class Employee {
}
```

---

Hibernate automatically determines the table name.

---

`@Table` becomes useful when:

```text
Custom Table Name Required
Schema Required
Catalog Required
Unique Constraints Required
Indexes Required
```

---

# Most Common Usage

```java
@Entity

@Table(name = "employees")
public class Employee {
}
```

---

Generated SQL:

```sql
SELECT *
FROM employees
```

---

# @Table Attributes

Main attributes:

```java
name
schema
catalog
uniqueConstraints
indexes
```

---

# 1. name Attribute

Most commonly used.

---

Example:

```java
@Entity

@Table(name = "employee_master")
public class Employee {
}
```

---

Table:

```sql
employee_master
```

---

# 2. schema Attribute

Used when database has multiple schemas.

---

Example:

```java
@Entity

@Table(
    name = "employees",
    schema = "hr"
)
public class Employee {
}
```

---

Generated Query:

```sql
SELECT *
FROM hr.employees
```

---

# 3. catalog Attribute

Used mostly in databases supporting catalogs.

---

Example:

```java
@Entity

@Table(
    name = "employees",
    catalog = "company_db"
)
public class Employee {
}
```

---

Less commonly used than schema.

---

# 4. uniqueConstraints

Creates unique constraints.

---

Example:

```java
@Entity

@Table(
    name = "employees",

    uniqueConstraints = {
        @UniqueConstraint(
            columnNames = "email"
        )
    }
)
public class Employee {
}
```

---

Generated SQL:

```sql
ALTER TABLE employees
ADD CONSTRAINT uk_email
UNIQUE(email);
```

---

Now:

```text
Duplicate Email Not Allowed
```

---

# Multiple Unique Constraints

```java
@Table(
    uniqueConstraints = {

        @UniqueConstraint(
            columnNames = "email"
        ),

        @UniqueConstraint(
            columnNames = {
                "first_name",
                "last_name"
            }
        )
    }
)
```

---

Generated:

```sql
UNIQUE(email)

UNIQUE(first_name,last_name)
```

---

# 5. indexes Attribute

Used to create DB indexes.

---

Example:

```java
@Entity

@Table(
    indexes = {
        @Index(
            name = "idx_email",
            columnList = "email"
        )
    }
)
public class Employee {
}
```

---

Generated SQL:

```sql
CREATE INDEX idx_email
ON employees(email);
```

---

Benefit:

```text
Faster Search Queries
```

---

# Multiple Indexes

```java
@Table(
    indexes = {

        @Index(
            name = "idx_email",
            columnList = "email"
        ),

        @Index(
            name = "idx_name",
            columnList = "name"
        )
    }
)
```

---

# Composite Index

```java
@Table(
    indexes = {
        @Index(
            name = "idx_name_dept",
            columnList =
            "name,department"
        )
    }
)
```

---

Generated:

```sql
CREATE INDEX idx_name_dept
ON employees(name,department);
```

---

# Complete Example

```java
@Entity

@Table(
    name = "employees",

    schema = "hr",

    uniqueConstraints = {
        @UniqueConstraint(
            columnNames = "email"
        )
    },

    indexes = {
        @Index(
            name = "idx_email",
            columnList = "email"
        )
    }
)
public class Employee {

    @Id
    private Long id;

    private String name;

    private String email;
}
```

---

# @Entity vs @Table

Most Asked Interview Question.

---

## @Entity

Marks class as JPA entity.

---

Example:

```java
@Entity
```

---

Mandatory.

---

## @Table

Customizes table mapping.

---

Example:

```java
@Table(name="employees")
```

---

Optional.

---

Comparison:

| Feature | @Entity | @Table |
|----------|----------|----------|
| Marks Entity | ✅ | ❌ |
| Table Name Mapping | ❌ | ✅ |
| Mandatory | ✅ | ❌ |

---

# @Table vs @Column

Interview Question.

---

## @Table

Controls:

```text
Table Level Configuration
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
Column Level Configuration
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

# Internal Hibernate Processing

Startup:

```java
@Entity

@Table(name="employees")
```

---

Hibernate Metadata:

```text
Entity Name
Table Name
Schema
Catalog
Indexes
Constraints
```

---

Stored in:

```java
SessionFactory
```

---

Used later for:

```sql
SELECT
INSERT
UPDATE
DELETE
```

---

# Common Mistakes

## Mistake 1

Using @Table Without @Entity

Wrong:

```java
@Table(name="employees")
public class Employee {
}
```

---

Ignored by Hibernate.

---

Must be:

```java
@Entity

@Table(name="employees")
```

---

# Mistake 2

Confusing Table Name With Entity Name

Wrong:

```java
@Entity(name="employees")
```

---

Entity name and table name are different concepts.

---

Use:

```java
@Table(name="employees")
```

for DB table mapping.

---

# Mistake 3

Creating Too Many Indexes

Wrong:

```text
Index Every Column
```

---

Problem:

```text
Slower Inserts
More Storage
```

---

Create indexes only when needed.

---

# Common Interview Questions

## What is @Table?

Used to customize entity-to-table mapping.

---

## Is @Table mandatory?

No.

---

## What happens without @Table?

Hibernate derives table name from entity class.

---

## Difference Between @Entity and @Table?

`@Entity`

```text
Marks Entity
```

---

`@Table`

```text
Configures Table
```

---

## Can @Table create indexes?

Yes.

Using:

```java
indexes
```

---

## Can @Table create unique constraints?

Yes.

Using:

```java
uniqueConstraints
```

---

## Can @Table define schema?

Yes.

Using:

```java
schema
```

---

# Real-World Production Example

```java
@Entity

@Table(
    name = "employees",

    uniqueConstraints = {
        @UniqueConstraint(
            columnNames = "email"
        )
    },

    indexes = {
        @Index(
            name = "idx_email",
            columnList = "email"
        )
    }
)
public class Employee {

    @Id
    @GeneratedValue
    private Long id;

    private String name;

    private String email;
}
```

---

Benefits:

```text
Custom Table Name
Unique Email
Fast Email Search
```

---

# Key Points To Remember

- `@Table` customizes database table mapping.
- Optional annotation.
- Works only with `@Entity`.
- `name` changes table name.
- `schema` defines DB schema.
- `catalog` defines DB catalog.
- `uniqueConstraints` creates unique keys.
- `indexes` creates database indexes.
- Table-level annotation.
- Metadata stored inside Hibernate SessionFactory.
- Very common Spring Data JPA interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Entity` | Marks JPA Entity |
| `@Table` | Table Configuration |
| `@Column` | Column Configuration |
| `@Id` | Primary Key |
| `@Index` | Database Index |
| `@UniqueConstraint` | Unique Constraint |

---

# Interview Shortcut

```java
@Entity

@Table(
    name = "employees",
    indexes = {
        @Index(
            name = "idx_email",
            columnList = "email"
        )
    }
)
public class Employee {
}
```

Meaning:

```text
Employee Entity
      ↓
employees Table
      ↓
Indexed By Email
      ↓
Faster Searches
```

This is one of the most commonly used JPA annotations in enterprise Spring Boot applications.