
## Definition

`@OneToMany` is a JPA annotation used to define a **one-to-many relationship** between two entities.

It tells Hibernate/JPA:

> One record in Entity A can be associated with multiple records in Entity B.

Package:

```java
import jakarta.persistence.OneToMany;
```

Prior to Spring Boot 3:

```java
import javax.persistence.OneToMany;
```

---

# What is a One-to-Many Relationship?

Real-world examples:

```text
Department → Employees
Customer → Orders
Country → States
Company → Projects
Teacher → Students
```

---

Example:

```text
One Department
      ↓
Many Employees
```

---

Database:

### DEPARTMENT

| ID | NAME |
|----|------|
| 1 | IT |

---

### EMPLOYEE

| ID | NAME | DEPARTMENT_ID |
|----|------|---------------|
| 101 | Sumit | 1 |
| 102 | Raj | 1 |
| 103 | Amit | 1 |

---

Relationship:

```text
Department 1 → Many Employees
```

---

# Why Do We Need @OneToMany?

Without relationships:

```java
private Long departmentId;
```

---

Problems:

```text
Manual Joins
No Object Navigation
No Cascading
More Boilerplate Code
```

---

With JPA:

```java
department.getEmployees();
```

directly.

---

# Basic Syntax

```java
@OneToMany
private List<Employee> employees;
```

---

Meaning:

```text
One Department
      ↓
Many Employees
```

---

# Unidirectional One-to-Many

Possible but not recommended.

---

## Employee

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String name;
}
```

---

## Department

```java
@Entity
public class Department {

    @Id
    private Long id;

    private String name;

    @OneToMany

    @JoinColumn(
        name = "department_id"
    )
    private List<Employee> employees;
}
```

---

Database:

### EMPLOYEE

| ID | NAME | DEPARTMENT_ID |
|----|------|---------------|
| 101 | Sumit | 1 |
| 102 | Raj | 1 |

---

Department knows Employees.

Employee does not know Department.

---

# Bidirectional One-to-Many

Most common approach.

---

## Department

```java
@Entity
public class Department {

    @Id
    private Long id;

    private String name;

    @OneToMany(
        mappedBy = "department"
    )
    private List<Employee> employees;
}
```

---

## Employee

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String name;

    @ManyToOne

    @JoinColumn(
        name = "department_id"
    )
    private Department department;
}
```

---

Now:

```java
department.getEmployees();
```

and

```java
employee.getDepartment();
```

both work.

---

# Why mappedBy?

Interview Favorite.

---

Example:

```java
@OneToMany(
    mappedBy = "department"
)
private List<Employee> employees;
```

---

Meaning:

```text
Relationship managed by
department field in Employee
```

---

Prevents:

```text
Duplicate Foreign Keys
Extra Join Tables
```

---

# Owning Side vs Inverse Side

Very Important.

---

## Owning Side

Contains:

```java
@ManyToOne

@JoinColumn
```

---

Example:

```java
@ManyToOne

@JoinColumn(
    name = "department_id"
)
private Department department;
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
@OneToMany(
    mappedBy = "department"
)
```

---

Only reflects relationship.

---

# Database Structure

Department:

```sql
DEPARTMENT
(
 ID,
 NAME
)
```

---

Employee:

```sql
EMPLOYEE
(
 ID,
 NAME,
 DEPARTMENT_ID
)
```

---

Notice:

```text
Foreign Key exists on MANY side.
```

---

This is a very important interview point.

---

# Cascade Operations

Common usage.

---

```java
@OneToMany(
    cascade = CascadeType.ALL
)
private List<Employee> employees;
```

---

Save Department:

```java
departmentRepository.save(department);
```

---

Employees saved automatically.

---

# Example

```java
Department dept =
    new Department();

dept.setEmployees(
    List.of(emp1, emp2)
);

departmentRepository.save(dept);
```

---

Hibernate inserts:

```text
Department
Employee 1
Employee 2
```

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

# Orphan Removal

Advanced Interview Topic.

---

```java
@OneToMany(
    mappedBy = "department",
    orphanRemoval = true
)
private List<Employee> employees;
```

---

Remove Employee:

```java
department
.getEmployees()
.remove(emp);
```

---

Hibernate:

```sql
DELETE FROM employee
```

---

Automatically removes orphan record.

---

# Fetch Types

Important Interview Topic.

---

Default:

```java
LAZY
```

for OneToMany.

---

Example:

```java
@OneToMany(
    mappedBy = "department"
)
private List<Employee> employees;
```

---

Employees NOT loaded immediately.

---

Loaded only when:

```java
department.getEmployees();
```

called.

---

# Explicit Lazy

```java
@OneToMany(
    fetch = FetchType.LAZY
)
```

---

Default behavior.

---

# Eager Loading

```java
@OneToMany(
    fetch = FetchType.EAGER
)
```

---

Department query loads:

```text
Department
+
All Employees
```

---

Can cause performance problems.

---

Generally avoid.

---

# Internal SQL Example

Department Query:

```java
departmentRepository.findById(1L);
```

---

SQL:

```sql
SELECT *
FROM department
WHERE id=1
```

---

Later:

```java
department.getEmployees();
```

---

SQL:

```sql
SELECT *
FROM employee
WHERE department_id=1
```

---

Lazy loading in action.

---

# @OneToMany vs @ManyToOne

Most Asked Interview Question.

---

## OneToMany

```text
One Department
      ↓
Many Employees
```

---

Collection Side.

---

Example:

```java
List<Employee>
```

---

## ManyToOne

```text
Many Employees
       ↓
One Department
```

---

Single Object Side.

---

Example:

```java
Department department;
```

---

Comparison:

| Feature | OneToMany | ManyToOne |
|----------|----------|-----------|
| Collection | ✅ | ❌ |
| Single Object | ❌ | ✅ |
| Default Fetch | LAZY | EAGER |
| FK Stored Here | ❌ | ✅ |

---

# @OneToMany Without mappedBy

Example:

```java
@OneToMany
private List<Employee> employees;
```

---

Hibernate may create:

```sql
DEPARTMENT_EMPLOYEE
```

join table.

---

Not ideal.

---

Prefer:

```java
mappedBy
```

for bidirectional relationships.

---

# Common Collection Types

Most common:

```java
List<Employee>
```

---

Possible:

```java
Set<Employee>
```

---

Example:

```java
@OneToMany(
    mappedBy = "department"
)
private Set<Employee> employees;
```

---

# Internal Hibernate Processing

Example:

```java
@OneToMany(
    mappedBy = "department"
)
private List<Employee> employees;
```

---

Hibernate Metadata:

```text
Relationship Type
Target Entity
Mapped By
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
Joins
Lazy Loading
Persistence
Cascade Operations
```

---

# Common Mistakes

## Mistake 1

Using EAGER Fetch

Wrong:

```java
@OneToMany(
    fetch = FetchType.EAGER
)
```

---

Large collections can kill performance.

---

Prefer:

```java
LAZY
```

---

# Mistake 2

Missing mappedBy

Wrong:

```java
@OneToMany
private List<Employee> employees;
```

---

May create unnecessary join tables.

---

# Mistake 3

Only Updating One Side

Wrong:

```java
department
.getEmployees()
.add(emp);
```

---

Also set:

```java
emp.setDepartment(department);
```

---

Keep both sides synchronized.

---

# Common Interview Questions

## What is @OneToMany?

Defines a one-to-many relationship.

---

## Where is the foreign key stored?

On the MANY side.

---

Example:

```sql
EMPLOYEE
(
 DEPARTMENT_ID
)
```

---

## Default Fetch Type?

```java
LAZY
```

---

## What is mappedBy?

Defines the inverse side of the relationship.

---

## Which side owns the relationship?

```java
@ManyToOne
```

side.

---

## What is orphanRemoval?

Deletes child records removed from collection.

---

## Can @OneToMany exist without @ManyToOne?

Yes.

But not recommended.

---

# Real-World Production Example

```java
@Entity
public class Department {

    @Id

    @GeneratedValue
    private Long id;

    private String name;

    @OneToMany(
        mappedBy = "department",
        cascade = CascadeType.ALL,
        orphanRemoval = true
    )
    private List<Employee> employees =
        new ArrayList<>();
}
```

---

```java
@Entity
public class Employee {

    @Id

    @GeneratedValue
    private Long id;

    private String name;

    @ManyToOne(
        fetch = FetchType.LAZY
    )

    @JoinColumn(
        name = "department_id"
    )
    private Department department;
}
```

---

Relationship:

```text
One Department
      ↓
Many Employees
```

---

Database:

```sql
EMPLOYEE
(
 ID,
 NAME,
 DEPARTMENT_ID
)
```

---

# Key Points To Remember

- `@OneToMany` represents a one-to-many relationship.
- Foreign key exists on the MANY side.
- Default fetch type is `LAZY`.
- Usually paired with `@ManyToOne`.
- `mappedBy` defines the inverse side.
- Owning side is typically the `@ManyToOne` side.
- Supports cascading.
- Supports orphan removal.
- Avoid EAGER fetching for large collections.
- One of the most important JPA relationship interview topics.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@OneToMany` | One Parent → Many Children |
| `@ManyToOne` | Many Children → One Parent |
| `@OneToOne` | One ↔ One |
| `@ManyToMany` | Many ↔ Many |
| `@JoinColumn` | Foreign Key Column |
| `mappedBy` | Inverse Side |

---

# Interview Shortcut

```java
@OneToMany(
    mappedBy = "department",
    cascade = CascadeType.ALL,
    orphanRemoval = true
)
private List<Employee> employees;
```

Meaning:

```text
One Department
      ↓
Many Employees
      ↓
Managed By department Field
      ↓
Cascade Enabled
      ↓
Orphan Records Removed
```

This is the most common enterprise implementation of a JPA one-to-many relationship.