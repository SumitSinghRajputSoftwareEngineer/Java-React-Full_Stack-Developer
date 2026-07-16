
## Definition

`@ManyToMany` is a JPA annotation used to define a **many-to-many relationship** between two entities.

It tells Hibernate/JPA:

> Many records of Entity A can be associated with many records of Entity B.

Package:

```java
import jakarta.persistence.ManyToMany;
```

Prior to Spring Boot 3:

```java
import javax.persistence.ManyToMany;
```

---

# What is a Many-to-Many Relationship?

Real-world examples:

```text
Students ↔ Courses
Users ↔ Roles
Employees ↔ Projects
Authors ↔ Books
Doctors ↔ Patients
```

---

Example:

```text
Many Students
        ↕
Many Courses
```

---

Database:

### STUDENT

| ID | NAME |
|----|------|
| 1 | Sumit |
| 2 | Raj |

---

### COURSE

| ID | NAME |
|----|------|
| 10 | Java |
| 20 | Spring Boot |

---

### STUDENT_COURSE

| STUDENT_ID | COURSE_ID |
|------------|-----------|
| 1 | 10 |
| 1 | 20 |
| 2 | 10 |

---

Relationship:

```text
Sumit → Java
Sumit → Spring Boot
Raj   → Java
```

---

# Why Do We Need @ManyToMany?

Without it:

```java
List<Long> courseIds;
```

---

Problems:

```text
Manual Join Queries
No Object Navigation
No Cascade Management
More Boilerplate Code
```

---

With JPA:

```java
student.getCourses();
```

and

```java
course.getStudents();
```

directly.

---

# How Many-to-Many Works Internally

Important Interview Point.

---

JPA cannot store:

```java
List<Course>
```

inside a table column.

---

So Hibernate creates:

```text
Join Table
```

---

Example:

```sql
STUDENT_COURSE
```

---

Contains:

```sql
STUDENT_ID
COURSE_ID
```

---

This join table manages the relationship.

---

# Basic Syntax

```java
@ManyToMany
private List<Course> courses;
```

---

Meaning:

```text
Many Students
       ↕
Many Courses
```

---

# Unidirectional Many-to-Many

Only Student knows Course.

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

## Student

```java
@Entity
public class Student {

    @Id
    private Long id;

    private String name;

    @ManyToMany

    @JoinTable(
        name = "student_course"
    )
    private List<Course> courses;
}
```

---

Database:

```sql
STUDENT
COURSE
STUDENT_COURSE
```

---

Navigation:

```java
student.getCourses();
```

Works.

---

```java
course.getStudents();
```

Not available.

---

# Understanding @JoinTable

Most Asked Interview Question.

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

Creates:

```sql
STUDENT_COURSE
```

---

Join Table.

---

# Custom Join Table

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

Generated:

```sql
STUDENT_COURSE
(
 STUDENT_ID,
 COURSE_ID
)
```

---

# Bidirectional Many-to-Many

Most common.

---

## Student

```java
@Entity
public class Student {

    @ManyToMany

    @JoinTable(
        name = "student_course"
    )
    private List<Course> courses;
}
```

---

## Course

```java
@Entity
public class Course {

    @ManyToMany(
        mappedBy = "courses"
    )
    private List<Student> students;
}
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

# What is mappedBy?

Interview Favorite.

---

Example:

```java
@ManyToMany(
    mappedBy = "courses"
)
private List<Student> students;
```

---

Meaning:

```text
Relationship managed by
courses field in Student
```

---

Prevents:

```text
Duplicate Join Tables
```

---

Without mappedBy:

Hibernate may create:

```sql
STUDENT_COURSE
COURSE_STUDENT
```

---

Wrong design.

---

# Owning Side vs Inverse Side

Important Concept.

---

## Owning Side

Contains:

```java
@JoinTable
```

---

Example:

```java
@ManyToMany

@JoinTable(...)
private List<Course> courses;
```

---

Responsible for:

```text
Join Table Updates
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
@ManyToMany(
    mappedBy = "courses"
)
```

---

Read-only relationship view.

---

# Fetch Type

Interview Question.

---

Default:

```java
LAZY
```

for ManyToMany.

---

Example:

```java
@ManyToMany
private List<Course> courses;
```

---

Courses loaded only when:

```java
student.getCourses();
```

called.

---

# Eager Fetch

```java
@ManyToMany(
    fetch = FetchType.EAGER
)
```

---

Loads:

```text
Student
+
All Courses
```

immediately.

---

Can hurt performance.

---

Generally avoid.

---

# Cascade Operations

Possible:

```java
@ManyToMany(
    cascade = CascadeType.ALL
)
```

---

But dangerous.

---

Example:

Delete Student.

Could delete:

```java
Course
```

used by many students.

---

Usually use:

```java
CascadeType.PERSIST
```

or

```java
CascadeType.MERGE
```

---

Avoid:

```java
REMOVE
ALL
```

in most ManyToMany cases.

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

(if cascaded)

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

# Example Data

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

# @ManyToMany vs @OneToMany

Interview Question.

---

## ManyToMany

```text
Many Students
        ↕
Many Courses
```

---

Requires:

```text
Join Table
```

---

## OneToMany

```text
One Department
       ↓
Many Employees
```

---

Requires:

```text
Foreign Key
```

---

Comparison:

| Feature | ManyToMany | OneToMany |
|----------|------------|-----------|
| Join Table | ✅ | ❌ |
| Foreign Key Only | ❌ | ✅ |
| Collection Both Sides | ✅ | Optional |
| Students-Courses | ✅ | ❌ |
| Department-Employees | ❌ | ✅ |

---

# Why ManyToMany Is Often Avoided

Very Important Production Interview Topic.

---

Suppose:

```text
Student ↔ Course
```

Later requirement:

```text
Enrollment Date
Grade
Status
```

---

Join table now needs extra columns.

---

Simple ManyToMany cannot handle this well.

---

Instead create:

```java
Enrollment
```

entity.

---

Example:

```java
Student
      ↓
Enrollment
      ↓
Course
```

---

Now:

```java
Enrollment
{
   enrollmentDate
   grade
   status
}
```

---

Production systems often prefer this approach.

---

# Internal Hibernate Processing

Example:

```java
@ManyToMany
private List<Course> courses;
```

---

Hibernate Metadata:

```text
Target Entity
Join Table
Join Columns
Fetch Strategy
Cascade Strategy
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
Lazy Loading
Persistence
Relationship Management
```

---

# Common Mistakes

## Mistake 1

Missing mappedBy

Results:

```text
Duplicate Join Tables
```

---

# Mistake 2

Using EAGER Fetch

Results:

```text
Huge Queries
Performance Problems
```

---

Prefer:

```java
LAZY
```

---

# Mistake 3

Using CascadeType.ALL

Deleting Student may affect Courses.

---

Usually avoid.

---

# Common Interview Questions

## What is @ManyToMany?

Defines a many-to-many relationship.

---

## How is it implemented?

Using a join table.

---

## What annotation creates the join table?

```java
@JoinTable
```

---

## Default Fetch Type?

```java
LAZY
```

---

## What is mappedBy?

Defines the inverse side.

---

## Which side owns the relationship?

The side containing:

```java
@JoinTable
```

---

## Why is ManyToMany avoided in production?

Because join tables often need additional attributes.

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

Database:

```sql
USER
ROLE
USER_ROLE
```

---

Relationship:

```text
Many Users
      ↕
Many Roles
```

---

# Key Points To Remember

- `@ManyToMany` represents many-to-many relationships.
- Internally implemented using a join table.
- `@JoinTable` defines the join table.
- `mappedBy` defines the inverse side.
- Default fetch type is `LAZY`.
- Owning side contains `@JoinTable`.
- Inverse side contains `mappedBy`.
- Avoid `CascadeType.ALL` in most cases.
- Often replaced by an intermediate entity in production systems.
- One of the most important JPA relationship interview topics.

---

# Quick Interview Shortcut

```java
@ManyToMany

@JoinTable(
    name = "student_course"
)
private List<Course> courses;
```

Meaning:

```text
Many Students
        ↕
Many Courses
        ↓
Join Table = student_course
        ↓
Relationship Managed Automatically
```

This is the standard JPA implementation of a many-to-many relationship.