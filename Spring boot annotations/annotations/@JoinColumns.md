
## Definition

`@JoinColumns` is a JPA annotation used when a relationship requires **multiple foreign key columns** instead of a single foreign key column.

It tells Hibernate/JPA:

> Use multiple columns together to establish the relationship between two entities.

Package:

```java
import jakarta.persistence.JoinColumns;
```

Prior to Spring Boot 3:

```java
import javax.persistence.JoinColumns;
```

---

# Why Do We Need @JoinColumns?

Normally:

```java
@JoinColumn(
    name = "department_id"
)
```

uses a single foreign key.

---

But sometimes the parent entity has:

```text
Composite Primary Key
```

or

```text
Composite Unique Key
```

---

Example:

```text
Country Code + State Code
```

together identify a State.

---

Single column is not enough.

---

Need:

```java
@JoinColumns
```

---

# Real-Life Example

Consider:

```text
Employee
      ↓
Office
```

---

Office identified by:

```text
office_id
company_id
```

---

Database:

### OFFICE

| OFFICE_ID | COMPANY_ID | NAME |
|------------|------------|------|
| 1 | 100 | Bangalore |
| 2 | 100 | Pune |

---

Composite Key:

```text
OFFICE_ID + COMPANY_ID
```

---

Employee table:

| ID | NAME | OFFICE_ID | COMPANY_ID |
|----|------|------------|------------|
| 1 | Sumit | 1 | 100 |

---

Need both columns.

---

Solution:

```java
@JoinColumns
```

---

# Basic Syntax

```java
@JoinColumns({
    @JoinColumn(...),
    @JoinColumn(...)
})
```

---

Example:

```java
@ManyToOne

@JoinColumns({

   @JoinColumn(
      name = "office_id",
      referencedColumnName = "office_id"
   ),

   @JoinColumn(
      name = "company_id",
      referencedColumnName = "company_id"
   )
})
private Office office;
```

---

Meaning:

```text
office_id
+
company_id

together form relationship
```

---

# Example Using Composite Primary Key

## OfficeId

```java
@Embeddable
public class OfficeId {

    private Long officeId;

    private Long companyId;
}
```

---

# Office

```java
@Entity
public class Office {

    @EmbeddedId
    private OfficeId id;

    private String name;
}
```

---

# Employee

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String name;

    @ManyToOne

    @JoinColumns({

        @JoinColumn(
            name = "office_id",
            referencedColumnName = "officeId"
        ),

        @JoinColumn(
            name = "company_id",
            referencedColumnName = "companyId"
        )
    })
    private Office office;
}
```

---

# Generated Table

### OFFICE

```sql
OFFICE
(
 OFFICE_ID,
 COMPANY_ID,
 NAME,

 PRIMARY KEY
 (
   OFFICE_ID,
   COMPANY_ID
 )
)
```

---

### EMPLOYEE

```sql
EMPLOYEE
(
 ID,
 NAME,

 OFFICE_ID,
 COMPANY_ID,

 FOREIGN KEY
 (
   OFFICE_ID,
   COMPANY_ID
 )

 REFERENCES OFFICE
 (
   OFFICE_ID,
   COMPANY_ID
 )
)
```

---

# Understanding referencedColumnName

Most Asked Interview Question.

---

Example:

```java
@JoinColumn(
    name = "office_id",

    referencedColumnName =
        "officeId"
)
```

---

Meaning:

```text
EMPLOYEE.office_id
references
OFFICE.officeId
```

---

Second:

```java
@JoinColumn(
    name = "company_id",

    referencedColumnName =
        "companyId"
)
```

---

Meaning:

```text
EMPLOYEE.company_id
references
OFFICE.companyId
```

---

Together:

```text
Composite Foreign Key
```

---

# Why Not Multiple @JoinColumn?

Wrong:

```java
@JoinColumn(...)
@JoinColumn(...)
```

---

Compilation Error.

---

Need wrapper:

```java
@JoinColumns({
    ...
})
```

---

because Java allows only one annotation instance of that type here.

---

# Internal SQL Generated

Save Employee:

```java
employeeRepository.save(emp);
```

---

Hibernate:

```sql
INSERT INTO employee
(
 id,
 name,
 office_id,
 company_id
)
VALUES
(
 1,
 'Sumit',
 1,
 100
)
```

---

Relationship resolved using:

```text
office_id + company_id
```

---

# @JoinColumns with @ManyToOne

Most common usage.

---

```java
@ManyToOne

@JoinColumns({
    @JoinColumn(...),
    @JoinColumn(...)
})
private Office office;
```

---

Relationship:

```text
Many Employees
        ↓
One Office
```

---

Composite FK.

---

# @JoinColumns with @OneToOne

Also possible.

---

```java
@OneToOne

@JoinColumns({
   @JoinColumn(...),
   @JoinColumn(...)
})
private Passport passport;
```

---

Used when target entity uses composite keys.

---

# Internal Hibernate Processing

Hibernate stores metadata:

```text
Join Column 1
Join Column 2
Referenced Columns
Foreign Key Mapping
```

---

During SQL generation:

```sql
JOIN
```

uses all columns.

---

Example:

```sql
SELECT *
FROM employee e

JOIN office o

ON e.office_id=o.office_id
AND
   e.company_id=o.company_id
```

---

Notice:

```text
Multiple Join Conditions
```

---

# @JoinColumn vs @JoinColumns

Interview Question.

---

## @JoinColumn

Single foreign key.

---

Example:

```java
@JoinColumn(
    name = "department_id"
)
```

---

Database:

```text
DEPARTMENT_ID
```

---

## @JoinColumns

Multiple foreign keys.

---

Example:

```java
@JoinColumns({
   @JoinColumn(...),
   @JoinColumn(...)
})
```

---

Database:

```text
OFFICE_ID
COMPANY_ID
```

---

Comparison:

| Feature | @JoinColumn | @JoinColumns |
|----------|------------|-------------|
| Single FK | ✅ | ❌ |
| Composite FK | ❌ | ✅ |
| Simple PK | ✅ | ❌ |
| Composite PK | ❌ | ✅ |

---

# Real Production Example

Suppose:

```text
Bank Account
```

identified by:

```text
account_number
branch_code
```

---

Customer references account.

---

Account:

```java
@Entity
public class Account {

    @EmbeddedId
    private AccountId id;
}
```

---

Customer:

```java
@Entity
public class Customer {

    @ManyToOne

    @JoinColumns({

        @JoinColumn(
            name = "account_number",
            referencedColumnName =
                "accountNumber"
        ),

        @JoinColumn(
            name = "branch_code",
            referencedColumnName =
                "branchCode"
        )
    })
    private Account account;
}
```

---

Relationship requires:

```text
Account Number
+
Branch Code
```

---

# Common Mistakes

## Mistake 1

Using @JoinColumn For Composite Keys

Wrong:

```java
@JoinColumn(
    name="office_id"
)
```

---

Missing:

```java
company_id
```

---

Relationship becomes incomplete.

---

# Mistake 2

Wrong referencedColumnName

Example:

```java
referencedColumnName="xyz"
```

---

Column doesn't exist.

---

Startup failure.

---

# Mistake 3

Forgetting Composite Key Mapping

Target entity must properly define:

```java
@EmbeddedId
```

or

```java
@IdClass
```

---

# Common Interview Questions

## What is @JoinColumns?

Used to define multiple join columns.

---

## Why is it needed?

For composite foreign keys.

---

## When is it used?

When referenced entity has:

```text
Composite Primary Key
```

or

```text
Composite Unique Key
```

---

## Can it be used with ManyToOne?

Yes.

Most common usage.

---

## Can it be used with OneToOne?

Yes.

---

## Difference Between @JoinColumn and @JoinColumns?

Single FK vs Composite FK.

---

# Real-World Production Example

```java
@ManyToOne

@JoinColumns({

    @JoinColumn(
        name = "office_id",
        referencedColumnName =
            "officeId"
    ),

    @JoinColumn(
        name = "company_id",
        referencedColumnName =
            "companyId"
    )
})
private Office office;
```

---

Meaning:

```text
Employee
      ↓
Office

Relationship Uses:

office_id
+
company_id
```

---

Generated SQL Join:

```sql
JOIN office

ON employee.office_id =
   office.office_id

AND

employee.company_id =
   office.company_id
```

---

# Key Points To Remember

- `@JoinColumns` groups multiple `@JoinColumn` annotations.
- Used for composite foreign keys.
- Commonly used with composite primary keys.
- Usually seen with `@EmbeddedId` or `@IdClass`.
- Can be used with `@ManyToOne` and `@OneToOne`.
- Hibernate generates joins using all specified columns.
- Required when one column is insufficient to identify the target entity.
- Frequently asked advanced JPA interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@JoinColumn` | Single Foreign Key |
| `@JoinColumns` | Composite Foreign Key |
| `@EmbeddedId` | Composite Primary Key |
| `@IdClass` | Composite Primary Key |
| `@ManyToOne` | Many → One Relationship |

---

# Interview Shortcut

```java
@JoinColumns({

   @JoinColumn(
      name = "office_id"
   ),

   @JoinColumn(
      name = "company_id"
   )
})
```

Meaning:

```text
Multiple Foreign Key Columns
            ↓
Composite Relationship
            ↓
Used With Composite Keys
            ↓
Both Columns Required
```

This is the standard JPA solution for mapping relationships that depend on composite primary keys or composite foreign keys.