
## Definition

`@JoinTable` is a JPA annotation used to define an **intermediate join table** that manages relationships between entities.

It tells Hibernate/JPA:

> Use this table to store and manage the relationship between two entities.

Package:

```java
import jakarta.persistence.JoinTable;
```

Prior to Spring Boot 3:

```java
import javax.persistence.JoinTable;
```

---

# Why Do We Need @JoinTable?

Some relationships cannot be represented using a single foreign key.

Example:

```text
Students ↔ Courses
```

---

One student can enroll in:

```text
Java
Spring
React
```

---

One course can have:

```text
100 Students
```

---

Database:

```text
Many Students
        ↕
Many Courses
```

---

Single foreign key is insufficient.

Need:

```text
Intermediate Table
```

---

Solution:

```java
@JoinTable
```

---

# What Exactly Does @JoinTable Do?

It creates:

```text
Relationship Table
```

between two entities.

---

Example:

```java
@ManyToMany

@JoinTable(
    name = "student_course"
)
private List<Course> courses;
```

---

Generated:

```sql
STUDENT_COURSE
(
 STUDENT_ID,
 COURSE_ID
)
```

---

# Real-Life Example

Student:

| ID | NAME |
|----|------|
| 1 | Sumit |

---

Course:

| ID | NAME |
|----|------|
| 10 | Java |
| 20 | Spring |

---

Join Table:

| STUDENT_ID | COURSE_ID |
|------------|-----------|
| 1 | 10 |
| 1 | 20 |

---

Meaning:

```text
Sumit enrolled in:

Java
Spring
```

---

# Basic Syntax

```java
@JoinTable(
    name = "student_course"
)
```

---

More common:

```java
@ManyToMany

@JoinTable(
    name = "student_course",

    joinColumns =
        @JoinColumn(
            name = "student_id"
        ),

    inverseJoinColumns =
        @JoinColumn(
            name = "course_id"
        )
)
private List<Course> courses;
```

---

# Understanding joinColumns

Interview Question.

---

Example:

```java
joinColumns =
    @JoinColumn(
        name = "student_id"
    )
```

---

Meaning:

```text
Current Entity Foreign Key
```

---

In:

```java
Student
```

entity:

```text
student_id
```

belongs to Student.

---

# Understanding inverseJoinColumns

Interview Favorite.

---

Example:

```java
inverseJoinColumns =
    @JoinColumn(
        name = "course_id"
    )
```

---

Meaning:

```text
Target Entity Foreign Key
```

---

References:

```java
Course
```

entity.

---

# Complete Example

## Student

```java
@Entity
public class Student {

    @Id
    private Long id;

    private String name;

    @ManyToMany

    @JoinTable(
        name = "student_course",

        joinColumns =
            @JoinColumn(
                name = "student_id"
            ),

        inverseJoinColumns =
            @JoinColumn(
                name = "course_id"
            )
    )
    private List<Course> courses;
}
```

---

## Course

```java
@Entity
public class Course {

    @Id
    private Long id;

    private String name;
}
```

---

Generated Tables

---

### STUDENT

```sql
ID
NAME
```

---

### COURSE

```sql
ID
NAME
```

---

### STUDENT_COURSE

```sql
STUDENT_ID
COURSE_ID
```

---

# Bidirectional ManyToMany

Most common.

---

## Student

```java
@ManyToMany

@JoinTable(
    name = "student_course"
)
private List<Course> courses;
```

---

## Course

```java
@ManyToMany(
    mappedBy = "courses"
)
private List<Student> students;
```

---

Now:

```java
student.getCourses();
```

and

```java
course.getStudents();
```

both work.

---

# Owning Side

Important Interview Question.

---

Side containing:

```java
@JoinTable
```

is:

```text
Owning Side
```

---

Responsible for:

```text
Insert
Update
Delete
```

in join table.

---

Example:

```java
@ManyToMany

@JoinTable(...)
private List<Course> courses;
```

---

Owns relationship.

---

# Inverse Side

Contains:

```java
mappedBy
```

---

Example:

```java
@ManyToMany(
    mappedBy = "courses"
)
```

---

Does NOT manage join table.

---

Only reads relationship.

---

# @JoinTable With @OneToMany

Possible.

---

Example:

```java
@OneToMany

@JoinTable(
    name = "department_employee"
)
private List<Employee> employees;
```

---

Generated:

```sql
DEPARTMENT_EMPLOYEE
```

---

But usually:

```java
@JoinColumn
```

is preferred.

---

# @JoinTable With @OneToOne

Also possible.

---

Rare.

---

Example:

```java
@OneToOne

@JoinTable(
    name = "person_passport"
)
```

---

Usually not needed.

---

# Common Attributes

## name

Join table name.

---

```java
@JoinTable(
    name = "student_course"
)
```

---

## joinColumns

Current entity foreign key.

---

```java
joinColumns =
   @JoinColumn(
      name="student_id"
   )
```

---

## inverseJoinColumns

Target entity foreign key.

---

```java
inverseJoinColumns =
   @JoinColumn(
      name="course_id"
   )
```

---

## foreignKey

Custom FK name.

---

```java
foreignKey =
    @ForeignKey(
        name="fk_student"
    )
```

---

# Internal SQL Example

Save Student:

```java
studentRepository.save(student);
```

---

Hibernate:

```sql
INSERT INTO student
```

---

```sql
INSERT INTO course
```

---

```sql
INSERT INTO student_course
(
 student_id,
 course_id
)
```

---

Join table stores relationships.

---

# Internal Hibernate Processing

Hibernate stores metadata:

```text
Join Table Name
Join Columns
Inverse Join Columns
Foreign Keys
```

---

Used for:

```text
Relationship Management
Join Queries
Persistence
```

---

# @JoinColumn vs @JoinTable

Very Important Interview Question.

---

## @JoinColumn

Creates:

```text
Foreign Key Column
```

---

Example:

```java
@ManyToOne

@JoinColumn(
    name="department_id"
)
```

---

Database:

```sql
EMPLOYEE
(
 DEPARTMENT_ID
)
```

---

## @JoinTable

Creates:

```text
Intermediate Table
```

---

Example:

```java
@ManyToMany

@JoinTable(
    name="student_course"
)
```

---

Database:

```sql
STUDENT_COURSE
(
 STUDENT_ID,
 COURSE_ID
)
```

---

Comparison:

| Feature | @JoinColumn | @JoinTable |
|----------|------------|------------|
| Foreign Key Column | ✅ | ❌ |
| Intermediate Table | ❌ | ✅ |
| ManyToMany | ❌ | ✅ |
| OneToOne | ✅ | Optional |
| ManyToOne | ✅ | Rare |

---

# Why ManyToMany Is Often Replaced

Advanced Production Topic.

---

Requirement:

```text
Student ↔ Course
```

Later:

```text
Enrollment Date
Grade
Status
```

---

Need extra columns.

---

Join table becomes:

```sql
STUDENT_COURSE
(
 STUDENT_ID,
 COURSE_ID,
 ENROLL_DATE,
 GRADE,
 STATUS
)
```

---

Simple ManyToMany is insufficient.

---

Create:

```java
Enrollment
```

entity instead.

---

Production architecture:

```text
Student
      ↓
Enrollment
      ↓
Course
```

---

Very common enterprise approach.

---

# Common Mistakes

## Mistake 1

Using @JoinTable on Both Sides

Wrong:

```java
Student
```

contains:

```java
@JoinTable
```

---

and

```java
Course
```

also contains:

```java
@JoinTable
```

---

May create duplicate join tables.

---

Only one owning side.

---

# Mistake 2

Missing mappedBy

Results:

```text
Duplicate Join Tables
```

---

# Mistake 3

Wrong joinColumns

Current entity FK and target entity FK swapped.

---

Relationship breaks.

---

# Common Interview Questions

## What is @JoinTable?

Defines an intermediate relationship table.

---

## Where is it mostly used?

```java
@ManyToMany
```

---

## What does joinColumns represent?

Current entity foreign key.

---

## What does inverseJoinColumns represent?

Target entity foreign key.

---

## Which side owns the relationship?

Side containing:

```java
@JoinTable
```

---

## Difference Between @JoinColumn and @JoinTable?

Foreign key column vs join table.

---

# Real-World Production Example

```java
@Entity
public class User {

    @Id

    @GeneratedValue
    private Long id;

    private String username;

    @ManyToMany(
        fetch = FetchType.LAZY
    )

    @JoinTable(
        name = "user_role",

        joinColumns =
            @JoinColumn(
                name = "user_id"
            ),

        inverseJoinColumns =
            @JoinColumn(
                name = "role_id"
            )
    )
    private Set<Role> roles =
        new HashSet<>();
}
```

---

```java
@Entity
public class Role {

    @Id

    @GeneratedValue
    private Long id;

    private String roleName;

    @ManyToMany(
        mappedBy = "roles"
    )
    private Set<User> users =
        new HashSet<>();
}
```

---

Generated Database:

```sql
USER
ROLE
USER_ROLE
```

---

Join Table:

```sql
USER_ROLE
(
 USER_ID,
 ROLE_ID
)
```

---

# Key Points To Remember

- `@JoinTable` creates an intermediate relationship table.
- Most commonly used with `@ManyToMany`.
- `joinColumns` = current entity FK.
- `inverseJoinColumns` = target entity FK.
- Owning side contains `@JoinTable`.
- Inverse side contains `mappedBy`.
- Hibernate manages join table automatically.
- Frequently replaced with a separate entity in production systems when extra attributes are required.
- One of the most important JPA interview topics.

---

# Quick Interview Shortcut

```java
@ManyToMany

@JoinTable(
    name = "student_course",

    joinColumns =
        @JoinColumn(
            name = "student_id"
        ),

    inverseJoinColumns =
        @JoinColumn(
            name = "course_id"
        )
)
```

Meaning:

```text
Student
    ↕
Course
    ↓
Join Table = student_course
    ↓
student_id → Student FK
course_id → Course FK
    ↓
Relationship Managed Automatically
```

This is the standard enterprise implementation of a JPA many-to-many relationship using a join table.