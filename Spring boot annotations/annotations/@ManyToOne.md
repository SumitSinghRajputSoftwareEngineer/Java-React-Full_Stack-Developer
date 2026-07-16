
## Definition

`@ManyToOne` is a JPA annotation used to define a **many-to-one relationship** between entities.

It tells Hibernate/JPA:

> Many records of Entity A can be associated with one record of Entity B.

Package:

```java
import jakarta.persistence.ManyToOne;
```

Prior to Spring Boot 3:

```java
import javax.persistence.ManyToOne;
```

---

# What is a Many-to-One Relationship?

Real-world examples:

```text
Many Employees → One Department
Many Orders → One Customer
Many Students → One College
Many Books → One Publisher
Many Products → One Category
```

---

Example:

```text
Many Employees
        ↓
One Department
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
Employee 101 → Department 1
Employee 102 → Department 1
Employee 103 → Department 1
```

---

# Why Do We Need @ManyToOne?

Without it:

```java
private Long departmentId;
```

Then:

```java
Department department =
    departmentRepository
        .findById(employee.getDepartmentId())
        .get();
```

Manual lookup required.

---

With JPA:

```java
employee.getDepartment();
```

directly.

---

# Basic Syntax

```java
@ManyToOne
private Department department;
```

---

Meaning:

```text
Many Employees
       ↓
One Department
```

---

# Simplest Example

## Department

```java
@Entity
public class Department {

    @Id
    private Long id;

    private String name;
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
    private Department department;
}
```

---

Hibernate creates:

```sql
EMPLOYEE
(
 ID,
 NAME,
 DEPARTMENT_ID
)
```

---

# Understanding @JoinColumn

Most common usage.

---

```java
@ManyToOne

@JoinColumn(
    name = "department_id"
)
private Department department;
```

---

Meaning:

```text
Create Foreign Key Column
department_id
```

---

Generated SQL:

```sql
EMPLOYEE
(
 ID,
 NAME,
 DEPARTMENT_ID
)
```

---

# How Data Looks

Department:

| ID | NAME |
|----|------|
| 1 | IT |

---

Employee:

| ID | NAME | DEPARTMENT_ID |
|----|------|---------------|
| 101 | Sumit | 1 |
| 102 | Raj | 1 |

---

Both employees belong to:

```text
Department ID 1
```

---

# Unidirectional Many-to-One

Employee knows Department.

Department does not know Employees.

---

```java
@Entity
public class Employee {

    @ManyToOne

    @JoinColumn(
        name = "department_id"
    )
    private Department department;
}
```

---

Navigation:

```java
employee.getDepartment();
```

Works.

---

```java
department.getEmployees();
```

Does NOT exist.

---

# Bidirectional Relationship

Most common in enterprise applications.

---

## Department

```java
@Entity
public class Department {

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
employee.getDepartment();
```

and

```java
department.getEmployees();
```

both work.

---

# Owning Side

Very Important Interview Question.

---

In:

```java
@OneToMany(
    mappedBy = "department"
)
```

and

```java
@ManyToOne

@JoinColumn(
    name = "department_id"
)
```

---

Owning side is:

```java
@ManyToOne
```

---

Because:

```java
@JoinColumn
```

exists here.

---

Responsible for:

```text
Foreign Key Updates
```

---

# Default Fetch Type

Frequently Asked.

---

For:

```java
@ManyToOne
```

Default is:

```java
FetchType.EAGER
```

---

Meaning:

```text
Department loaded immediately
```

---

Example:

```java
Employee emp =
    repository.findById(1L)
              .get();
```

---

Hibernate loads:

```text
Employee
+
Department
```

---

Automatically.

---

# Lazy Loading

Recommended in many projects.

---

```java
@ManyToOne(
    fetch = FetchType.LAZY
)
private Department department;
```

---

Now:

```java
Employee emp =
    repository.findById(1L)
              .get();
```

---

Only Employee loaded.

---

Department loaded when:

```java
emp.getDepartment();
```

is called.

---

# Optional Attribute

Default:

```java
optional = true
```

---

Meaning:

```text
Department can be NULL
```

---

Example:

```java
@ManyToOne(
    optional = false
)
```

---

Generated:

```sql
NOT NULL
```

foreign key.

---

Employee must belong to a department.

---

# Cascade Operations

Possible but use carefully.

---

```java
@ManyToOne(
    cascade = CascadeType.ALL
)
private Department department;
```

---

Be careful.

---

Deleting Employee:

```java
employeeRepository.delete(emp);
```

---

May delete Department too.

---

Usually NOT recommended.

---

Most common:

```java
@ManyToOne(
    fetch = FetchType.LAZY
)
```

without cascade.

---

# SQL Generated

Save Employee:

```java
employeeRepository.save(emp);
```

---

Hibernate:

```sql
INSERT INTO employee
(
 name,
 department_id
)
VALUES
(
 'Sumit',
 1
);
```

---

Foreign key stored:

```text
department_id = 1
```

---

# Internal Query Example

Fetch Employee:

```java
employeeRepository.findById(1L);
```

---

EAGER:

```sql
SELECT *
FROM employee e

LEFT JOIN department d
ON e.department_id=d.id
```

---

LAZY:

```sql
SELECT *
FROM employee
```

---

Later:

```java
emp.getDepartment();
```

---

Then:

```sql
SELECT *
FROM department
WHERE id=?
```

---

# @ManyToOne vs @OneToMany

Most Asked Interview Question.

---

## ManyToOne

```text
Many Employees
       ↓
One Department
```

---

Field Type:

```java
Department department;
```

---

Foreign key exists here.

---

## OneToMany

```text
One Department
      ↓
Many Employees
```

---

Field Type:

```java
List<Employee>
```

---

Comparison:

| Feature | ManyToOne | OneToMany |
|----------|-----------|-----------|
| Single Object | ✅ | ❌ |
| Collection | ❌ | ✅ |
| FK Stored Here | ✅ | ❌ |
| Default Fetch | EAGER | LAZY |
| Owning Side | ✅ | ❌ |

---

# Internal Hibernate Processing

Example:

```java
@ManyToOne

@JoinColumn(
    name="department_id"
)
private Department department;
```

---

Hibernate stores metadata:

```text
Target Entity
Join Column
Fetch Strategy
Cascade Strategy
```

---

Used for:

```text
Join Queries
Persistence
Lazy Loading
Foreign Key Updates
```

---

# Common Mistakes

## Mistake 1

Using EAGER Everywhere

Default:

```java
EAGER
```

---

Can cause:

```text
N+1 Query Problems
Performance Issues
```

---

Prefer:

```java
FetchType.LAZY
```

---

# Mistake 2

CascadeType.ALL on ManyToOne

Danger:

```java
Delete Employee
```

may delete:

```java
Department
```

---

Usually avoid.

---

# Mistake 3

Only Updating One Side

Wrong:

```java
employee.setDepartment(dept);
```

---

For bidirectional mapping also do:

```java
dept.getEmployees()
    .add(employee);
```

---

Keep both sides synchronized.

---

# Common Interview Questions

## What is @ManyToOne?

Defines a many-to-one relationship.

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
EAGER
```

---

## Which side owns the relationship?

```java
@ManyToOne
```

side.

---

## What does @JoinColumn do?

Creates the foreign key column.

---

## Can @ManyToOne be LAZY?

Yes.

```java
fetch = FetchType.LAZY
```

---

## Is CascadeType.ALL recommended?

Usually no.

---

# Real-World Production Example

```java
@Entity
public class Employee {

    @Id

    @GeneratedValue
    private Long id;

    private String name;

    @ManyToOne(
        fetch = FetchType.LAZY,
        optional = false
    )

    @JoinColumn(
        name = "department_id"
    )
    private Department department;
}
```

---

```java
@Entity
public class Department {

    @Id

    @GeneratedValue
    private Long id;

    private String name;

    @OneToMany(
        mappedBy = "department"
    )
    private List<Employee> employees =
        new ArrayList<>();
}
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

Relationship:

```text
Many Employees
        ↓
One Department
```

---

# Key Points To Remember

- `@ManyToOne` represents many records associated with one parent record.
- Foreign key is stored on the MANY side.
- `@JoinColumn` defines the foreign key column.
- Default fetch type is `EAGER`.
- Prefer `LAZY` in production for performance.
- Usually the owning side of the relationship.
- Commonly paired with `@OneToMany`.
- Avoid `CascadeType.ALL` in most ManyToOne mappings.
- One of the most important JPA relationship interview topics.

---

# Quick Interview Shortcut

```java
@ManyToOne(
    fetch = FetchType.LAZY
)

@JoinColumn(
    name = "department_id"
)
private Department department;
```

Meaning:

```text
Many Employees
       ↓
One Department
       ↓
Foreign Key = department_id
       ↓
Employee Owns Relationship
       ↓
Department Loaded Lazily
```

This is the most common enterprise implementation of a JPA many-to-one relationship.