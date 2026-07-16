
## Definition

`@MapsId` is a JPA annotation used to indicate that an entity's primary key value should be **derived from another associated entity**.

It tells Hibernate/JPA:

> Use the primary key of the parent entity as the primary key of this entity.

Package:

```java
import jakarta.persistence.MapsId;
```

Prior to Spring Boot 3:

```java
import javax.persistence.MapsId;
```

---

# Why Do We Need @MapsId?

Normally:

```java
Person
```

and

```java
Passport
```

have separate IDs.

---

Example:

### PERSON

| ID | NAME |
|----|------|
| 1 | Sumit |

---

### PASSPORT

| ID | NUMBER |
|----|--------|
| 100 | P12345 |

---

Relationship:

```text
Person ID    = 1
Passport ID  = 100
```

Different IDs.

---

Sometimes business rules require:

```text
Person ID = Passport ID
```

---

Example:

### PERSON

| ID | NAME |
|----|------|
| 1 | Sumit |

---

### PASSPORT

| ID | NUMBER |
|----|--------|
| 1 | P12345 |

---

Same primary key.

---

Solution:

```java
@MapsId
```

---

# What Exactly Does @MapsId Do?

It maps:

```text
Foreign Key
+
Primary Key
```

to the same column.

---

Meaning:

```text
Child Entity PK
=
Parent Entity PK
```

---

This is called:

```text
Shared Primary Key Association
```

---

# Real-Life Examples

```text
Person ↔ Passport

User ↔ UserProfile

Employee ↔ EmployeeDetail

Vehicle ↔ Registration
```

---

Each child record:

```text
Cannot exist
Without Parent
```

---

Perfect use case for:

```java
@MapsId
```

---

# Basic Syntax

```java
@MapsId
```

---

Usually used with:

```java
@OneToOne
```

---

Example:

```java
@OneToOne

@MapsId
private Person person;
```

---

Meaning:

```text
Use Person ID
as Passport ID
```

---

# Simple Example

## Parent Entity

### Person

```java
@Entity
public class Person {

    @Id

    @GeneratedValue
    private Long id;

    private String name;
}
```

---

## Child Entity

### Passport

```java
@Entity
public class Passport {

    @Id
    private Long id;

    private String passportNumber;

    @OneToOne

    @MapsId

    @JoinColumn(
        name = "id"
    )
    private Person person;
}
```

---

# Generated Database

### PERSON

```sql
ID
NAME
```

---

### PASSPORT

```sql
ID
PASSPORT_NUMBER
```

---

Notice:

```text
PASSPORT.ID
```

is both:

```text
Primary Key
AND
Foreign Key
```

---

# Relationship Diagram

```text
PERSON
-------
ID = 1

      │

      ▼

PASSPORT
---------
ID = 1
```

---

Same ID.

---

# Saving Data

```java
Person person =
    new Person();

person.setName("Sumit");

personRepository.save(person);
```

---

Generated:

```text
Person ID = 1
```

---

Create Passport:

```java
Passport passport =
    new Passport();

passport.setPassportNumber(
    "P12345"
);

passport.setPerson(person);

passportRepository.save(passport);
```

---

Hibernate automatically:

```text
Passport ID = 1
```

---

No need:

```java
passport.setId(...)
```

---

Because:

```java
@MapsId
```

copies Person ID.

---

# Internal SQL

Insert Person:

```sql
INSERT INTO person
(
 name
)
VALUES
(
 'Sumit'
)
```

---

Generated:

```text
ID = 1
```

---

Insert Passport:

```sql
INSERT INTO passport
(
 id,
 passport_number
)
VALUES
(
 1,
 'P12345'
)
```

---

Notice:

```text
id comes from Person
```

---

# Why Not Use @GeneratedValue?

Wrong:

```java
@Id

@GeneratedValue
private Long id;
```

---

and

```java
@MapsId
```

together.

---

Reason:

```text
Two ID Generators
```

trying to control same field.

---

Typically:

```java
@Id
private Long id;
```

---

without:

```java
@GeneratedValue
```

in child entity.

---

# @MapsId With Composite Keys

Advanced Interview Topic.

---

Possible with:

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
@MapsId("employeeId")
```

---

Maps only one portion of composite key.

---

# Example Using Composite Key

## EmployeeId

```java
@Embeddable
public class EmployeeId {

    private Long employeeId;

    private Long companyId;
}
```

---

## EmployeeDetail

```java
@Entity
public class EmployeeDetail {

    @EmbeddedId
    private EmployeeId id;

    @MapsId("employeeId")

    @ManyToOne
    private Employee employee;
}
```

---

Meaning:

```text
employeeId part
comes from Employee
```

---

# @MapsId vs @JoinColumn

Interview Favorite.

---

## @JoinColumn

Defines:

```text
Foreign Key
```

---

Example:

```java
@JoinColumn(
    name = "person_id"
)
```

---

Database:

```sql
PERSON_ID
```

---

Separate from PK.

---

## @MapsId

Defines:

```text
PK = FK
```

---

Database:

```sql
ID
```

acts as both.

---

Comparison:

| Feature | @JoinColumn | @MapsId |
|----------|------------|---------|
| FK Column | ✅ | ✅ |
| Shared PK | ❌ | ✅ |
| One-To-One | Common | Very Common |
| Composite Key Support | ✅ | ✅ |

---

# @MapsId vs @PrimaryKeyJoinColumn

Advanced Interview Question.

---

Both create:

```text
Shared Primary Key
```

---

Modern JPA recommendation:

```java
@MapsId
```

---

Preferred because:

```text
Cleaner
More Flexible
Better Composite Key Support
```

---

# Internal Hibernate Processing

Hibernate detects:

```java
@MapsId
```

---

Metadata:

```text
Parent Entity
Parent PK
Child PK
Relationship
```

---

During persist:

```text
Copy Parent PK
→ Child PK
```

Automatically.

---

# Common Mistakes

## Mistake 1

Using GeneratedValue in Child

Wrong:

```java
@Id

@GeneratedValue
private Long id;
```

---

With:

```java
@MapsId
```

---

Conflicting ID generation.

---

# Mistake 2

Not Setting Parent

Wrong:

```java
Passport passport =
    new Passport();

passportRepository.save(passport);
```

---

Result:

```text
Null Identifier Error
```

---

Must set:

```java
passport.setPerson(person);
```

---

# Mistake 3

Wrong Join Column

Wrong:

```java
@JoinColumn(
    name="person_id"
)
```

---

When PK column is:

```java
id
```

---

Causes mapping confusion.

---

# Common Interview Questions

## What is @MapsId?

Maps entity primary key from another entity.

---

## What is Shared Primary Key?

Parent and child share same ID value.

---

## Where is @MapsId commonly used?

```java
@OneToOne
```

relationships.

---

## Does @MapsId create a foreign key?

Yes.

And the FK is also the PK.

---

## Can @MapsId be used with composite keys?

Yes.

---

## Why use @MapsId?

To model entities that cannot exist independently.

---

# Real-World Production Example

## User

```java
@Entity
public class User {

    @Id

    @GeneratedValue
    private Long id;

    private String username;
}
```

---

## UserProfile

```java
@Entity
public class UserProfile {

    @Id
    private Long id;

    private String address;

    private String phone;

    @OneToOne

    @MapsId

    @JoinColumn(
        name = "id"
    )
    private User user;
}
```

---

Database:

### USER

```sql
ID
USERNAME
```

---

### USER_PROFILE

```sql
ID
ADDRESS
PHONE
```

---

Relationship:

```text
USER.ID
=
USER_PROFILE.ID
```

---

Shared Primary Key.

---

# Key Points To Remember

- `@MapsId` maps a child entity's primary key from a parent entity.
- Commonly used in One-To-One relationships.
- Creates a shared primary key association.
- Child PK = Parent PK.
- Eliminates duplicate ID columns.
- Works with `@JoinColumn`.
- Supports composite keys.
- Avoid `@GeneratedValue` in the child entity.
- Preferred over `@PrimaryKeyJoinColumn` in modern JPA.
- Important advanced JPA interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@JoinColumn` | Foreign Key |
| `@MapsId` | Shared Primary Key |
| `@EmbeddedId` | Composite PK |
| `@IdClass` | Composite PK |
| `@OneToOne` | Common Pair With @MapsId |

---

# Interview Shortcut

```java
@OneToOne

@MapsId

@JoinColumn(
    name = "id"
)
private Person person;
```

Meaning:

```text
Passport
      ↓
Person
      ↓
Passport ID = Person ID
      ↓
Same Column Acts As:
Primary Key + Foreign Key
```

This is the standard JPA approach for implementing a shared primary key one-to-one relationship.