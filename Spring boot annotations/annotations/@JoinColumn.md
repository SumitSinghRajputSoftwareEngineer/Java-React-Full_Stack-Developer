
## Definition

`@JoinColumn` is a JPA annotation used to define the **foreign key column** that links two entities.

It tells Hibernate/JPA:

> Use this database column to establish the relationship between entities.

Package:

```java
import jakarta.persistence.JoinColumn;
```

Prior to Spring Boot 3:

```java
import javax.persistence.JoinColumn;
```

---

# Why Do We Need @JoinColumn?

Consider:

```java
Employee
```

belongs to:

```java
Department
```

---

Database:

```text
Many Employees
       ↓
One Department
```

---

Employee Table:

```sql
EMPLOYEE
(
 ID,
 NAME,
 DEPARTMENT_ID
)
```

---

Question:

```text
Which column stores the foreign key?
```

Answer:

```java
@JoinColumn
```

---

# What Exactly Does It Do?

Example:

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
department_id
```

is the foreign key column.

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
Employee
      ↓
Department
```

---

# Basic Syntax

```java
@JoinColumn(
    name = "department_id"
)
```

---

Most common usage:

```java
@ManyToOne

@JoinColumn(
    name = "department_id"
)
private Department department;
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

    @JoinColumn(
        name = "department_id"
    )
    private Department department;
}
```

---

Generated SQL:

```sql
CREATE TABLE employee
(
 id BIGINT,

 name VARCHAR(255),

 department_id BIGINT,

 FOREIGN KEY
 (department_id)

 REFERENCES department(id)
);
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

---

Hibernate:

```java
employee.getDepartment();
```

returns:

```java
Department(1, "IT")
```

---

# Default Behavior Without @JoinColumn

Example:

```java
@ManyToOne
private Department department;
```

---

Hibernate generates column automatically.

Usually:

```sql
department_id
```

---

But explicit naming is recommended.

---

Best Practice:

```java
@JoinColumn(
    name = "department_id"
)
```

---

# Common Attributes

```java
name
referencedColumnName
nullable
unique
insertable
updatable
foreignKey
```

---

# 1. name

Most common attribute.

---

```java
@JoinColumn(
    name = "department_id"
)
```

---

Creates:

```sql
DEPARTMENT_ID
```

column.

---

# 2. referencedColumnName

Interview Question.

---

Default:

```text
References Primary Key
```

---

Example:

```java
@JoinColumn(
    name = "department_code",

    referencedColumnName = "code"
)
```

---

Department:

```java
@Entity
public class Department {

    @Id
    private Long id;

    private String code;
}
```

---

Foreign key points to:

```sql
DEPARTMENT.CODE
```

instead of:

```sql
DEPARTMENT.ID
```

---

# 3. nullable

Controls null values.

---

```java
@JoinColumn(
    nullable = false
)
```

---

Generated:

```sql
NOT NULL
```

---

Meaning:

```text
Relationship Required
```

---

Employee must belong to Department.

---

# 4. unique

Used mainly in One-To-One.

---

Example:

```java
@OneToOne

@JoinColumn(
    unique = true
)
```

---

Generated:

```sql
UNIQUE
```

constraint.

---

Ensures:

```text
One Record
      ↔
One Record
```

---

# 5. insertable

Controls INSERT.

---

```java
@JoinColumn(
    insertable = false
)
```

---

Hibernate ignores column during INSERT.

---

Rarely used.

---

# 6. updatable

Controls UPDATE.

---

```java
@JoinColumn(
    updatable = false
)
```

---

Foreign key cannot be modified.

---

Useful for immutable relationships.

---

# 7. foreignKey

Customize foreign key name.

---

```java
@JoinColumn(
    name = "department_id",

    foreignKey =
       @ForeignKey(
          name = "fk_emp_dept"
       )
)
```

---

Generated:

```sql
CONSTRAINT fk_emp_dept
```

---

# @JoinColumn in @ManyToOne

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

Database:

```sql
EMPLOYEE
(
 DEPARTMENT_ID
)
```

---

# @JoinColumn in @OneToOne

Example:

```java
@OneToOne

@JoinColumn(
    name = "passport_id"
)
private Passport passport;
```

---

Database:

```sql
PERSON
(
 PASSPORT_ID
)
```

---

Relationship:

```text
Person ↔ Passport
```

---

# @JoinColumn in Unidirectional OneToMany

Possible:

```java
@OneToMany

@JoinColumn(
    name = "department_id"
)
private List<Employee> employees;
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

Less common.

---

# Owning Side

Critical Interview Question.

---

`@JoinColumn` exists on:

```text
Owning Side
```

---

Examples:

### ManyToOne

```java
@ManyToOne

@JoinColumn(...)
```

Owns relationship.

---

### OneToOne

```java
@OneToOne

@JoinColumn(...)
```

Owns relationship.

---

Responsible for:

```text
Foreign Key Updates
```

---

# @JoinColumn vs mappedBy

Interview Favorite.

---

## @JoinColumn

```text
Owning Side
```

---

Creates:

```text
Foreign Key
```

---

Example:

```java
@JoinColumn(
    name="department_id"
)
```

---

## mappedBy

```text
Inverse Side
```

---

Example:

```java
@OneToMany(
    mappedBy="department"
)
```

---

Does NOT create foreign key.

---

Comparison:

| Feature | @JoinColumn | mappedBy |
|----------|------------|-----------|
| Creates FK | ✅ | ❌ |
| Owning Side | ✅ | ❌ |
| Updates FK | ✅ | ❌ |
| Inverse Side | ❌ | ✅ |

---

# Internal SQL Example

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
)
```

---

department_id comes from:

```java
@JoinColumn
```

---

# Internal Hibernate Processing

Example:

```java
@JoinColumn(
    name="department_id"
)
```

---

Hibernate Metadata:

```text
Column Name
Referenced Entity
Referenced Column
Nullability
Foreign Key Name
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
Relationship Management
```

---

# Common Mistakes

## Mistake 1

Using @JoinColumn on Both Sides

Wrong:

```java
@OneToMany
@JoinColumn(...)
```

and

```java
@ManyToOne
@JoinColumn(...)
```

---

May create mapping issues.

---

One side should own.

---

# Mistake 2

Forgetting mappedBy

Bidirectional relationship:

```java
@OneToMany
private List<Employee> employees;
```

---

May create extra join tables.

---

# Mistake 3

Wrong referencedColumnName

Example:

```java
referencedColumnName="xyz"
```

---

Column doesn't exist.

---

Application startup failure.

---

# Common Interview Questions

## What is @JoinColumn?

Defines the foreign key column.

---

## Which side contains @JoinColumn?

Owning side.

---

## Does @JoinColumn create a foreign key?

Yes.

---

## Is @JoinColumn mandatory?

No.

Hibernate can generate names automatically.

---

## What is referencedColumnName?

Specifies which column is referenced.

---

## Difference Between @JoinColumn and @JoinTable?

`@JoinColumn`

```text
Foreign Key Column
```

---

`@JoinTable`

```text
Intermediate Join Table
```

---

Used in:

```java
@ManyToMany
```

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
        fetch = FetchType.LAZY
    )

    @JoinColumn(
        name = "department_id",
        nullable = false,

        foreignKey =
            @ForeignKey(
                name = "fk_emp_dept"
            )
    )
    private Department department;
}
```

---

Generated SQL:

```sql
EMPLOYEE
(
 ID,
 NAME,
 DEPARTMENT_ID NOT NULL,

 CONSTRAINT fk_emp_dept
 FOREIGN KEY(DEPARTMENT_ID)
 REFERENCES DEPARTMENT(ID)
)
```

---

# Key Points To Remember

- `@JoinColumn` defines a foreign key column.
- Used in `@ManyToOne`, `@OneToOne`, and some `@OneToMany` mappings.
- Usually placed on the owning side.
- `name` defines the FK column name.
- `referencedColumnName` defines the referenced column.
- `nullable=false` creates NOT NULL constraints.
- `unique=true` is common in One-To-One relationships.
- Creates and manages foreign key relationships.
- Different from `mappedBy`.
- One of the most important JPA interview topics.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@JoinColumn` | Foreign Key Column |
| `@JoinTable` | Join Table |
| `mappedBy` | Inverse Side |
| `@ManyToOne` | Many → One |
| `@OneToOne` | One ↔ One |

---

# Interview Shortcut

```java
@ManyToOne

@JoinColumn(
    name = "department_id"
)
private Department department;
```

Meaning:

```text
Employee
    ↓
Department
    ↓
Foreign Key Column
department_id
    ↓
Relationship Owned Here
```

This is the most common usage of `@JoinColumn` in Spring Boot and Hibernate applications.