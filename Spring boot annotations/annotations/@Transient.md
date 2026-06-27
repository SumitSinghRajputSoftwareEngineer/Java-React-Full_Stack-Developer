
## Definition

`@Transient` is a JPA annotation used to tell Hibernate/JPA:

> Do NOT persist this field to the database.

The field exists in the Java object but is ignored during:

```text
INSERT
UPDATE
SELECT Mapping
Schema Generation
```

Package:

```java
import jakarta.persistence.Transient;
```

Prior to Spring Boot 3:

```java
import javax.persistence.Transient;
```

---

# Why Do We Need @Transient?

Sometimes a field is useful in Java code but should not be stored in the database.

Examples:

```text
Full Name
Age Calculation
Temporary Values
Session Data
UI Data
Calculated Fields
Passwords Confirmation
```

---

Example:

```java
firstName = "Sumit"
lastName  = "Rajput"
```

Need:

```java
fullName = "Sumit Rajput"
```

---

Should fullName be stored?

```text
No
```

It can be calculated.

---

Use:

```java
@Transient
private String fullName;
```

---

# Basic Syntax

```java
@Transient
private String fullName;
```

---

Meaning:

```text
Field exists in Java object
But no DB column is created
```

---

# Basic Example

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String firstName;

    private String lastName;

    @Transient
    private String fullName;
}
```

---

Generated Table:

```sql
EMPLOYEE
(
    ID,
    FIRST_NAME,
    LAST_NAME
)
```

---

Notice:

```text
FULL_NAME column is NOT created
```

---

# Example: Calculated Field

```java
@Entity
public class Employee {

    private String firstName;

    private String lastName;

    @Transient
    public String getFullName() {

        return firstName +
               " " +
               lastName;
    }
}
```

---

Database:

```sql
FIRST_NAME
LAST_NAME
```

---

No:

```sql
FULL_NAME
```

---

# Password Confirmation Example

Registration Request:

```java
@Entity
public class User {

    private String username;

    private String password;

    @Transient
    private String confirmPassword;
}
```

---

Database:

```sql
USERNAME
PASSWORD
```

---

Not Stored:

```sql
CONFIRM_PASSWORD
```

---

Good Security Practice.

---

# OTP Example

```java
@Entity
public class User {

    private String email;

    @Transient
    private String otp;
}
```

---

OTP:

```text
Temporary Runtime Data
```

---

No need to store in DB.

---

# Shopping Cart Example

```java
@Entity
public class Order {

    private BigDecimal amount;

    @Transient
    private Integer itemCount;
}
```

---

itemCount:

```text
Calculated at runtime
```

---

Not persisted.

---

# What Happens During INSERT?

Entity:

```java
@Entity
public class Employee {

    private String name;

    @Transient
    private String fullName;
}
```

---

Object:

```java
Employee e =
    new Employee();

e.setName("Sumit");

e.setFullName(
    "Sumit Rajput"
);
```

---

Hibernate SQL:

```sql
INSERT INTO employee
(name)
VALUES
('Sumit');
```

---

Notice:

```text
fullName ignored
```

---

# What Happens During UPDATE?

```java
employee.setFullName(
    "Updated Name"
);
```

---

Hibernate:

```text
Does NOT update DB
```

---

Because:

```java
@Transient
```

---

# What Happens During SELECT?

Database:

```sql
EMPLOYEE
```

contains:

```sql
ID
NAME
```

---

Hibernate loads:

```java
Employee
```

---

Field:

```java
@Transient
private String fullName;
```

---

Value:

```java
null
```

unless calculated manually.

---

# @Transient on Method

Can also be used on getters.

---

Example:

```java
@Entity
public class Employee {

    private String firstName;

    private String lastName;

    @Transient
    public String getFullName() {

        return firstName +
               " " +
               lastName;
    }
}
```

---

No DB column created.

---

# JPA @Transient vs Java transient

Very Important Interview Question.

---

## JPA @Transient

```java
@Transient
private String fullName;
```

---

Meaning:

```text
Ignored By Hibernate/JPA
```

---

Still available for:

```text
Serialization
JSON Conversion
Java Usage
```

---

## Java transient

```java
private transient String fullName;
```

---

Meaning:

```text
Ignored During Java Serialization
```

---

Affects:

```java
ObjectOutputStream
```

---

Comparison:

| Feature | @Transient | transient |
|----------|------------|------------|
| JPA Ignore | ✅ | ❌ |
| Serialization Ignore | ❌ | ✅ |
| Database Mapping Ignore | ✅ | ❌ |

---

# Using Both Together

Possible:

```java
@Transient
private transient String token;
```

---

Meaning:

```text
Not Stored In DB
Not Serialized
```

---

# @Transient vs @Column(insertable=false, updatable=false)

Interview Question.

---

## @Transient

Field completely ignored.

---

```java
@Transient
private String fullName;
```

---

No column exists.

---

## insertable=false, updatable=false

```java
@Column(
    insertable = false,
    updatable = false
)
private String status;
```

---

Column exists.

---

Hibernate reads it.

---

But doesn't modify it.

---

Comparison:

| Feature | @Transient | insertable/updatable |
|----------|------------|---------------------|
| Column Exists | ❌ | ✅ |
| Read From DB | ❌ | ✅ |
| Written To DB | ❌ | ❌ |

---

# Internal Hibernate Processing

Example:

```java
@Transient
private String fullName;
```

---

Startup Metadata:

```text
Ignore This Field
```

---

Hibernate Stores:

```text
ID
NAME
EMAIL
```

---

Ignores:

```text
FULL_NAME
```

---

Not added to:

```text
DDL
INSERT
UPDATE
SELECT
```

---

# Internal Architecture

```text
Entity Scan
     │
     ▼
Field Found
     │
     ▼
@Transient ?
     │
 ┌──Yes────┐
 ▼         ▼
Ignore   Map Column
```

---

# Common Mistakes

## Mistake 1

Expecting Value To Persist

Wrong:

```java
@Transient
private String fullName;
```

---

Save:

```java
employeeRepository.save(emp);
```

---

Reload:

```java
fullName = null
```

---

Expected behavior.

---

# Mistake 2

Using @Transient For Sensitive Data

Wrong:

```java
@Transient
private String password;
```

---

Then expecting DB storage.

---

Not persisted.

---

# Mistake 3

Confusing With Java transient

Wrong:

```java
transient
```

thinking Hibernate ignores it.

---

Use:

```java
@Transient
```

for JPA.

---

# Common Interview Questions

## What is @Transient?

Marks a field as non-persistent.

---

## Does Hibernate create a column?

No.

---

## Is field stored in DB?

No.

---

## Can @Transient be used on methods?

Yes.

---

## Difference Between @Transient and transient?

`@Transient`

```text
JPA/Hibernate Ignore
```

---

`transient`

```text
Java Serialization Ignore
```

---

## When is @Transient used?

```text
Calculated Fields
Temporary Data
OTP
Confirm Password
UI Values
Runtime Data
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

    private String firstName;

    private String lastName;

    @Transient
    private String fullName;

    public String getFullName() {

        return firstName +
               " " +
               lastName;
    }
}
```

---

Generated Table:

```sql
EMPLOYEES
(
 ID,
 FIRST_NAME,
 LAST_NAME
)
```

---

No:

```sql
FULL_NAME
```

---

# Key Points To Remember

- `@Transient` excludes a field from persistence.
- No database column is created.
- Ignored during INSERT and UPDATE.
- Ignored during schema generation.
- Commonly used for calculated values.
- Useful for OTPs, tokens, and confirmation passwords.
- Can be applied to fields or getter methods.
- Different from Java `transient`.
- Hibernate completely ignores transient fields.
- Very common JPA and Hibernate interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Transient` | Ignore Field In DB |
| `@Column` | Map Field To Column |
| `@Id` | Primary Key |
| `@GeneratedValue` | Auto Generate ID |
| `transient` | Ignore Java Serialization |

---

# Interview Shortcut

```java
@Entity
public class Employee {

    private String firstName;

    private String lastName;

    @Transient
    private String fullName;
}
```

Meaning:

```text
Field Exists In Java Object
        ↓
Hibernate Ignores It
        ↓
No Database Column
        ↓
Used Only At Runtime
```

This is one of the most frequently used annotations for calculated and temporary fields in JPA applications.