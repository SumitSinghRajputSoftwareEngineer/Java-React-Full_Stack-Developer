
## Definition

`@OneToOne` is a JPA annotation used to define a **one-to-one relationship** between two entities.

It tells Hibernate/JPA:

> One record in Entity A is associated with exactly one record in Entity B.

Package:

```java
import jakarta.persistence.OneToOne;
```

Prior to Spring Boot 3:

```java
import javax.persistence.OneToOne;
```

---

# What is a One-to-One Relationship?

Real-world examples:

```text
Person ↔ Passport
Employee ↔ Locker
User ↔ Profile
Citizen ↔ Aadhaar Card
Vehicle ↔ Registration Certificate
```

---

Example:

```text
One Person → One Passport
One Passport → One Person
```

---

Database:

### PERSON

| ID | NAME |
|----|------|
| 1 | Sumit |

---

### PASSPORT

| ID | NUMBER | PERSON_ID |
|----|---------|-----------|
| 101 | P12345 | 1 |

---

Relationship:

```text
PERSON 1 ↔ 1 PASSPORT
```

---

# Why Do We Need @OneToOne?

Without relationships:

```java
private Long passportId;
```

---

Problems:

```text
Manual Joins
No Object Navigation
More Boilerplate Code
No Cascading
```

---

Solution:

```java
@OneToOne
```

---

Allows:

```java
employee.getPassport();
```

directly.

---

# Basic Syntax

```java
@OneToOne
private Passport passport;
```

---

Meaning:

```text
One Employee
      ↔
One Passport
```

---

# Unidirectional One-to-One

Most basic example.

---

## Passport Entity

```java
@Entity
public class Passport {

    @Id
    private Long id;

    private String passportNumber;
}
```

---

## Person Entity

```java
@Entity
public class Person {

    @Id
    private Long id;

    private String name;

    @OneToOne

    @JoinColumn(
        name = "passport_id"
    )
    private Passport passport;
}
```

---

Generated Table:

### PERSON

| ID | NAME | PASSPORT_ID |
|----|------|-------------|
| 1 | Sumit | 101 |

---

### PASSPORT

| ID | PASSPORT_NUMBER |
|----|----------------|
| 101 | P12345 |

---

Relationship:

```text
Person knows Passport
Passport doesn't know Person
```

---

Called:

```text
Unidirectional Relationship
```

---

# Understanding @JoinColumn

Most Asked Interview Question.

---

Example:

```java
@OneToOne

@JoinColumn(
    name = "passport_id"
)
private Passport passport;
```

---

Generated:

```sql
PASSPORT_ID
```

inside PERSON table.

---

Meaning:

```text
Foreign Key Column
```

---

Database:

```sql
PERSON
(
 ID,
 NAME,
 PASSPORT_ID
)
```

---

# Bidirectional One-to-One

Both entities know each other.

---

## Person

```java
@Entity
public class Person {

    @Id
    private Long id;

    @OneToOne

    @JoinColumn(
        name = "passport_id"
    )
    private Passport passport;
}
```

---

## Passport

```java
@Entity
public class Passport {

    @Id
    private Long id;

    @OneToOne(
        mappedBy = "passport"
    )
    private Person person;
}
```

---

Now:

```java
person.getPassport();
```

and

```java
passport.getPerson();
```

both work.

---

# What is mappedBy?

Interview Favorite.

---

Example:

```java
@OneToOne(
    mappedBy = "passport"
)
private Person person;
```

---

Meaning:

```text
Relationship managed by
passport field in Person
```

---

Prevents:

```text
Duplicate Foreign Keys
```

---

Without mappedBy:

Hibernate creates:

```text
Two Foreign Keys
```

which is wrong.

---

# Owning Side vs Inverse Side

Important Interview Concept.

---

## Owning Side

Contains:

```java
@JoinColumn
```

---

Example:

```java
@OneToOne

@JoinColumn(
    name="passport_id"
)
private Passport passport;
```

---

Responsible for:

```text
Foreign Key Updates
```

---

## Inverse Side

Contains:

```java
mappedBy
```

---

Example:

```java
@OneToOne(
    mappedBy="passport"
)
```

---

Only reflects relationship.

---

# Cascade Operations

Frequently used.

---

Example:

```java
@OneToOne(
    cascade =
    CascadeType.ALL
)
private Passport passport;
```

---

Save Person:

```java
personRepository.save(person);
```

---

Passport saved automatically.

---

Equivalent:

```java
passportRepository.save(passport);
```

performed internally.

---

# Cascade Types

```java
PERSIST
MERGE
REMOVE
REFRESH
DETACH
ALL
```

---

Most common:

```java
CascadeType.ALL
```

---

# Example

```java
@OneToOne(
    cascade =
    CascadeType.ALL
)
private Passport passport;
```

---

Save:

```java
personRepository.save(person);
```

---

Both records inserted.

---

# Fetch Types

Important Interview Topic.

---

```java
@OneToOne(
    fetch =
    FetchType.EAGER
)
```

---

Default:

```text
EAGER
```

---

Meaning:

```text
Load Passport immediately
```

---

Query:

```java
personRepository.findById(1L);
```

---

Hibernate loads:

```text
Person
+
Passport
```

---

# Lazy Loading

```java
@OneToOne(
    fetch =
    FetchType.LAZY
)
```

---

Passport loaded only when:

```java
person.getPassport();
```

called.

---

Useful for performance.

---

# Optional Relationship

Default:

```java
optional = true
```

---

Meaning:

```text
Passport may be null
```

---

Example:

```java
@OneToOne(
    optional = false
)
```

---

Meaning:

```text
Passport required
```

---

Generated:

```sql
NOT NULL
```

foreign key.

---

# Shared Primary Key One-to-One

Advanced Interview Topic.

---

Passport ID = Person ID

---

Person:

```java
@Entity
public class Person {

    @Id
    private Long id;
}
```

---

Passport:

```java
@Entity
public class Passport {

    @Id
    private Long id;

    @OneToOne

    @MapsId
    private Person person;
}
```

---

Database:

```text
Person ID = Passport ID
```

---

Very efficient design.

---

# Internal SQL Example

Save:

```java
personRepository.save(person);
```

---

Hibernate:

```sql
INSERT INTO passport
```

---

Then:

```sql
INSERT INTO person
(
 id,
 passport_id
)
```

---

# @OneToOne vs @ManyToOne

Interview Question.

---

## OneToOne

```text
One Person
↔
One Passport
```

---

Database:

```text
Unique Foreign Key
```

---

## ManyToOne

```text
Many Employees
→
One Department
```

---

Database:

```text
Repeated Foreign Keys
```

---

Comparison:

| Feature | OneToOne | ManyToOne |
|----------|----------|-----------|
| One Record Per Relation | ✅ | ❌ |
| Unique FK | ✅ | ❌ |
| Passport Example | ✅ | ❌ |
| Department Example | ❌ | ✅ |

---

# Internal Hibernate Processing

Example:

```java
@OneToOne
private Passport passport;
```

---

Hibernate Metadata:

```text
Association Type
Target Entity
Join Column
Fetch Type
Cascade Type
```

---

Stored inside:

```java
SessionFactory
```

---

Used for:

```text
Join Queries
Persistence
Cascade Operations
Lazy Loading
```

---

# Common Mistakes

## Mistake 1

Missing mappedBy

Bidirectional relationship without:

```java
mappedBy
```

---

Results:

```text
Duplicate Foreign Keys
```

---

# Mistake 2

Using EAGER Everywhere

Problem:

```text
Extra Queries
Performance Issues
```

---

Prefer:

```java
FetchType.LAZY
```

when appropriate.

---

# Mistake 3

Forgetting Cascade

Save parent:

```java
personRepository.save(person);
```

---

Passport not saved.

---

Use:

```java
cascade = CascadeType.ALL
```

---

# Common Interview Questions

## What is @OneToOne?

Defines a one-to-one relationship between entities.

---

## What is the default fetch type?

```java
EAGER
```

---

## What is mappedBy?

Specifies the inverse side of a bidirectional relationship.

---

## Which side owns the relationship?

The side containing:

```java
@JoinColumn
```

---

## What is @JoinColumn?

Creates the foreign key column.

---

## Can @OneToOne be lazy loaded?

Yes.

```java
fetch = FetchType.LAZY
```

---

## What is the default optional value?

```java
true
```

---

# Real-World Production Example

```java
@Entity
public class User {

    @Id

    @GeneratedValue
    private Long id;

    private String username;

    @OneToOne(
        cascade = CascadeType.ALL,
        fetch = FetchType.LAZY
    )

    @JoinColumn(
        name = "profile_id"
    )
    private UserProfile profile;
}
```

---

```java
@Entity
public class UserProfile {

    @Id

    @GeneratedValue
    private Long id;

    private String address;

    private String phone;
}
```

---

Database:

```sql
USER
(
 ID,
 USERNAME,
 PROFILE_ID
)
```

---

Relationship:

```text
One User
     ↔
One Profile
```

---

# Key Points To Remember

- `@OneToOne` creates a one-to-one relationship.
- Common examples: User-Profile, Person-Passport.
- `@JoinColumn` defines the foreign key.
- `mappedBy` defines the inverse side.
- Default fetch type is `EAGER`.
- Supports `LAZY` loading.
- Supports cascading.
- Owning side contains `@JoinColumn`.
- Inverse side contains `mappedBy`.
- Supports shared primary key mapping using `@MapsId`.
- One of the most important JPA relationship interview topics.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@OneToOne` | One-to-One Relationship |
| `@ManyToOne` | Many-to-One Relationship |
| `@OneToMany` | One-to-Many Relationship |
| `@ManyToMany` | Many-to-Many Relationship |
| `@JoinColumn` | Foreign Key Column |
| `@MapsId` | Shared Primary Key |

---

# Interview Shortcut

```java
@OneToOne(
    cascade = CascadeType.ALL,
    fetch = FetchType.LAZY
)

@JoinColumn(
    name = "passport_id"
)
private Passport passport;
```

Meaning:

```text
One Person
      ↔
One Passport
      ↓
Foreign Key = passport_id
      ↓
Lazy Loaded
      ↓
Cascade Enabled
```

This is the most common enterprise implementation of a JPA one-to-one relationship.