
## Definition

`@Entity` is a JPA annotation used to mark a Java class as a database entity.

It tells JPA/Hibernate:

> This class should be mapped to a database table.

Package:

```java
import jakarta.persistence.Entity;
```

Prior to Spring Boot 3:

```java
import javax.persistence.Entity;
```

---

# Why Do We Need @Entity?

Without `@Entity`, Hibernate does not know:

```text
Which classes should be persisted
Which classes map to database tables
Which objects should be stored in DB
```

---

Example:

```java
public class Employee {

    private Long id;
    private String name;
}
```

Hibernate sees:

```text
Just a normal Java class
```

---

Cannot create:

```text
Table
Insert Query
Update Query
Delete Query
Select Query
```

---

Add:

```java
@Entity
```

Now Hibernate knows:

```text
This class maps to a database table.
```

---

# Basic Syntax

```java
@Entity
public class Employee {

}
```

---

Equivalent Meaning:

```text
Employee ↔ Database Table
```

---

# Simplest Example

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String name;
}
```

---

Hibernate Creates:

```sql
EMPLOYEE
```

Table:

| ID | NAME |
|----|------|
| 1 | Sumit |

---

# Internal Flow

```text
Application Start
        │
        ▼
Spring Boot
        │
        ▼
Scan @Entity Classes
        │
        ▼
Create Metadata
        │
        ▼
Generate Tables
        │
        ▼
Ready For CRUD
```

---

# Example With Table

```java
@Entity
public class Student {

    @Id
    private Long id;

    private String name;

    private Integer age;
}
```

---

Hibernate Creates:

```sql
STUDENT
```

---

Columns:

```sql
ID
NAME
AGE
```

---

# @Entity + @Table

By default:

```java
@Entity
public class Employee {
}
```

Table Name:

```sql
employee
```

(or EMPLOYEE depending on DB)

---

Custom Table:

```java
@Entity

@Table(name = "employees")
public class Employee {
}
```

---

Now table becomes:

```sql
employees
```

---

# Mandatory Requirement

Every Entity should have:

```java
@Id
```

---

Example:

```java
@Entity
public class Employee {

    @Id
    private Long id;
}
```

---

Without:

```java
@Id
```

Application startup fails.

---

Exception:

```text
No identifier specified for entity
```

---

# Primary Key Example

```java
@Entity
public class Employee {

    @Id

    @GeneratedValue
    private Long id;

    private String name;
}
```

---

Insert:

```java
employeeRepository.save(
    new Employee(null, "Sumit")
);
```

---

Hibernate:

```sql
INSERT INTO employee
```

Generates:

```text
ID automatically
```

---

# How Hibernate Detects Entities

Spring Boot scans:

```text
Main Package
Sub Packages
```

---

Example:

```java
com.company
    |
    +-- entity
          |
          Employee
```

---

```java
@Entity
public class Employee {
}
```

Detected automatically.

---

# Entity Lifecycle

Interview Favorite.

---

States:

```text
1. Transient
2. Persistent
3. Detached
4. Removed
```

---

## 1. Transient

Object exists only in memory.

```java
Employee emp =
    new Employee();
```

---

Not stored in DB.

---

## 2. Persistent

```java
entityManager.persist(emp);
```

---

Managed by Hibernate.

---

Stored in DB.

---

## 3. Detached

```java
entityManager.detach(emp);
```

---

Not managed anymore.

---

Still exists in DB.

---

## 4. Removed

```java
entityManager.remove(emp);
```

---

Marked for deletion.

---

# Entity Example

```java
@Entity
public class Product {

    @Id

    @GeneratedValue
    private Long id;

    private String name;

    private BigDecimal price;
}
```

---

Generated Table:

```sql
PRODUCT
(
 ID,
 NAME,
 PRICE
)
```

---

# Default Constructor Requirement

JPA requires:

```java
No-Argument Constructor
```

---

Example:

```java
@Entity
public class Employee {

    public Employee() {
    }
}
```

---

Why?

Hibernate creates objects using reflection.

---

Without it:

```text
InstantiationException
```

may occur.

---

# Can Entity Be Final?

Wrong:

```java
@Entity
public final class Employee {
}
```

---

Avoid.

---

Why?

Hibernate uses:

```text
Proxy Objects
Lazy Loading
```

---

Final classes break proxy creation.

---

# Can Entity Have Business Logic?

Yes.

---

Example:

```java
@Entity
public class Account {

    private BigDecimal balance;

    public void deposit(
            BigDecimal amount) {

        balance =
            balance.add(amount);
    }
}
```

---

Allowed.

---

But keep:

```text
Heavy Business Logic
```

inside:

```java
@Service
```

---

# @Entity vs @Component

Interview Question.

---

## @Entity

Purpose:

```text
Database Mapping
```

---

Managed By:

```text
Hibernate/JPA
```

---

Example:

```java
@Entity
public class Employee {
}
```

---

## @Component

Purpose:

```text
Spring Bean
```

---

Managed By:

```text
Spring Container
```

---

Example:

```java
@Component
public class EmployeeService {
}
```

---

Comparison:

| Feature | @Entity | @Component |
|----------|----------|-----------|
| DB Table | ✅ | ❌ |
| Spring Bean | ❌ | ✅ |
| Managed By Hibernate | ✅ | ❌ |
| Managed By Spring | ❌ | ✅ |

---

# @Entity vs @Table

Interview Question.

---

## @Entity

Marks class as entity.

```java
@Entity
```

---

Mandatory.

---

## @Table

Customizes table.

```java
@Table(name="employees")
```

---

Optional.

---

Example:

```java
@Entity

@Table(name="employees")
public class Employee {
}
```

---

# @Entity vs DTO

Interview Question.

---

## Entity

Represents:

```text
Database Structure
```

---

Example:

```java
@Entity
public class Employee {
}
```

---

## DTO

Represents:

```text
API Request/Response
```

---

Example:

```java
public class EmployeeRequest {
}
```

---

Never expose entities directly in large enterprise applications.

---

# Internal Hibernate Processing

At Startup:

```java
@Entity
class Employee
```

---

Hibernate creates:

```text
Entity Metadata
```

Contains:

```text
Class Name
Table Name
Columns
Relationships
Primary Key
```

---

Stored inside:

```java
SessionFactory
```

---

# Common Mistakes

## Mistake 1

Missing @Id

Wrong:

```java
@Entity
public class Employee {

    private Long id;
}
```

---

Startup Failure.

---

Use:

```java
@Id
private Long id;
```

---

# Mistake 2

No Default Constructor

Wrong:

```java
@Entity
public class Employee {

    public Employee(
            String name) {
    }
}
```

---

Add:

```java
public Employee() {
}
```

---

# Mistake 3

Using Entity As API Response

Wrong:

```java
@GetMapping
public Employee getEmployee()
```

---

Prefer:

```java
EmployeeResponse DTO
```

---

# Common Interview Questions

## What is @Entity?

Marks a class as a JPA entity.

---

## What does Hibernate do with @Entity?

Maps it to a database table.

---

## Is @Id mandatory?

Yes.

Every entity requires a primary key.

---

## Is @Table mandatory?

No.

Optional.

---

## Can Entity Exist Without Default Constructor?

Not recommended.

JPA expects one.

---

## Can Entity Be Final?

Avoid.

Hibernate proxies may fail.

---

## Who Manages Entity?

```text
Hibernate/JPA
```

---

## Difference Between Entity and DTO?

Entity:

```text
Database Representation
```

DTO:

```text
API Representation
```

---

# Real-World Production Example

```java
@Entity

@Table(name = "employees")
public class Employee {

    @Id

    @GeneratedValue(
        strategy =
        GenerationType.IDENTITY
    )
    private Long id;

    private String name;

    private String email;

    private BigDecimal salary;

    public Employee() {
    }
}
```

---

Repository:

```java
public interface EmployeeRepository
        extends JpaRepository<
            Employee,
            Long> {
}
```

---

Save:

```java
employeeRepository.save(
    new Employee()
);
```

---

Hibernate:

```sql
INSERT INTO employees
```

Automatically executes.

---

# Key Points To Remember

- `@Entity` marks a class as a JPA entity.
- Entity maps to a database table.
- Managed by Hibernate/JPA.
- Every entity requires an `@Id`.
- `@Table` is optional.
- Needs a no-argument constructor.
- Should not be final.
- Hibernate scans entities during startup.
- Entity lifecycle: Transient → Persistent → Detached → Removed.
- Metadata stored inside SessionFactory.
- Most important JPA annotation and a very common interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Entity` | Marks JPA Entity |
| `@Table` | Custom Table Name |
| `@Id` | Primary Key |
| `@GeneratedValue` | Auto ID Generation |
| `@Column` | Column Customization |
| `@Transient` | Ignore Field |

---

# Interview Shortcut

```java
@Entity
@Table(name = "employees")
public class Employee {

    @Id
    @GeneratedValue
    private Long id;

    private String name;
}
```

Meaning:

```text
Employee Object
        ⇅
Hibernate/JPA
        ⇅
employees Table
```

This is the foundation of Spring Data JPA and Hibernate persistence.