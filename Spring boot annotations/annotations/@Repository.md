
## Definition

`@Repository` is a specialized Spring stereotype annotation used to mark a class as a Data Access Layer (DAO) component.

It tells Spring:

> This class is responsible for interacting with the database and should be managed as a Spring Bean.

Internally:

```java
@Repository
```

is a specialization of:

```java
@Component
```

Therefore, every repository is automatically:

- Discovered during component scanning
- Registered as a Spring Bean
- Available for Dependency Injection

---

# Why Do We Need @Repository?

In a layered architecture:

```text
Controller
    │
    ▼
Service
    │
    ▼
Repository
    │
    ▼
Database
```

The Repository Layer is responsible for:

- CRUD Operations
- Database Queries
- JPA Operations
- JDBC Operations
- Data Retrieval
- Data Persistence

Instead of marking DAO classes with:

```java
@Component
```

Spring provides:

```java
@Repository
```

to indicate:

```text
"This class performs database operations."
```

---

# Basic Syntax

```java
@Repository
public class EmployeeRepository {

}
```

---

# What Happens Internally?

During startup:

```java
@ComponentScan
```

finds:

```java
@Repository
public class EmployeeRepository {
}
```

Since:

```java
@Repository
```

contains:

```java
@Component
```

Spring:

### Step 1

Creates Bean Definition.

---

### Step 2

Registers Bean.

---

### Step 3

Instantiates Object.

---

### Step 4

Performs Dependency Injection.

---

### Step 5

Stores Bean in IoC Container.

---

Flow:

```text
@Repository
       │
       ▼
Component Scan
       │
       ▼
Bean Definition
       │
       ▼
Bean Creation
       │
       ▼
Dependency Injection
       │
       ▼
Bean Ready
```

---

# Internal Structure

Simplified version:

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Component
public @interface Repository {

}
```

Notice:

```java
@Component
```

inside.

Thus:

```java
@Repository
```

inherits all behavior of:

```java
@Component
```

---

# Traditional DAO Example

Before Spring Data JPA:

```java
@Repository
public class EmployeeRepository {

    public Employee findById(Long id) {

        // JDBC Logic

        return employee;

    }

}
```

---

Usage:

```java
@Service
public class EmployeeService {

    private final EmployeeRepository repository;

    public EmployeeService(
            EmployeeRepository repository) {

        this.repository = repository;
    }

}
```

---

# Spring Data JPA Example

Most common modern usage.

---

## Entity

```java
@Entity
public class Employee {

    @Id
    private Long id;

}
```

---

## Repository

```java
@Repository
public interface EmployeeRepository
        extends JpaRepository<Employee, Long> {

}
```

---

Spring automatically generates implementation.

No code required.

---

# Is @Repository Required With JpaRepository?

Technically:

```java
No
```

because Spring Data automatically registers repository interfaces.

Example:

```java
public interface EmployeeRepository
        extends JpaRepository<Employee, Long> {

}
```

works perfectly.

---

However, many teams still add:

```java
@Repository
```

for readability.

---

# Bean Naming

Default Bean Name:

```java
@Repository
public class EmployeeRepository {

}
```

Bean Name:

```text
employeeRepository
```

---

# Custom Bean Name

```java
@Repository("empRepo")
public class EmployeeRepository {

}
```

Bean Name:

```text
empRepo
```

---

# Dependency Injection Example

```java
@Repository
public class EmployeeRepository {

}
```

---

```java
@Service
public class EmployeeService {

    private final EmployeeRepository repository;

    public EmployeeService(
            EmployeeRepository repository) {

        this.repository = repository;
    }

}
```

Spring injects repository automatically.

---

# Most Important Feature:
# Exception Translation

This is what makes `@Repository` different from `@Component`.

---

# Problem Without Spring

Database operations may throw:

```java
SQLException
```

Example:

```java
java.sql.SQLException
```

Vendor-specific exceptions:

```java
MySQLSyntaxErrorException
```

```java
SQLServerException
```

```java
OracleDatabaseException
```

Different databases throw different exceptions.

---

# Spring Solution

`@Repository` enables:

```text
Persistence Exception Translation
```

Spring converts vendor-specific exceptions into:

```java
DataAccessException
```

hierarchy.

---

Example:

Database throws:

```java
SQLException
```

Spring converts to:

```java
DataIntegrityViolationException
```

or

```java
DuplicateKeyException
```

or

```java
CannotAcquireLockException
```

depending on the error.

---

# Why Is Exception Translation Useful?

Without translation:

```java
catch(SQLException e)
```

Database-dependent code.

---

With Spring:

```java
catch(DataAccessException e)
```

Database-independent code.

---

This makes applications portable.

---

# Example

```java
@Repository
public class EmployeeRepository {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    public void save(Employee employee) {

        jdbcTemplate.update(
                "INSERT INTO employee VALUES (?,?)",
                employee.getId(),
                employee.getName()
        );

    }

}
```

---

If insert fails:

```java
SQLException
```

becomes:

```java
DataAccessException
```

automatically.

---

# How Exception Translation Works

Spring creates an AOP proxy around repository beans.

Flow:

```text
Repository Method
       │
       ▼
SQLException
       │
       ▼
Spring Proxy
       │
       ▼
DataAccessException
       │
       ▼
Caller
```

---

# @Repository vs @Component

Most common interview question.

---

## @Component

Generic Bean.

```java
@Component
public class EmployeeRepository {

}
```

---

## @Repository

DAO Bean.

```java
@Repository
public class EmployeeRepository {

}
```

---

Extra Feature:

```text
Exception Translation
```

---

| Feature | @Component | @Repository |
|----------|------------|-------------|
| Spring Bean | Yes | Yes |
| Component Scan | Yes | Yes |
| Dependency Injection | Yes | Yes |
| Exception Translation | No | Yes |
| Semantic Meaning | Generic | DAO Layer |

---

# @Repository vs @Service

---

## Repository

Responsible for:

```text
Database Access
```

---

## Service

Responsible for:

```text
Business Logic
```

---

Architecture:

```text
Controller
     │
     ▼
Service
     │
     ▼
Repository
     │
     ▼
Database
```

---

# Repository Responsibilities

Should contain:

- CRUD Operations
- Queries
- Database Access
- Entity Retrieval
- Persistence Logic

Should NOT contain:

- Business Rules
- Validation
- Workflow Logic

Those belong in Service Layer.

---

# JPA Repository Example

```java
@Repository
public interface EmployeeRepository
        extends JpaRepository<Employee, Long> {

    List<Employee> findByDepartment(
            String department);

}
```

Spring generates implementation automatically.

---

# Custom Query Example

```java
@Repository
public interface EmployeeRepository
        extends JpaRepository<Employee, Long> {

    @Query("""
           select e
           from Employee e
           where e.salary > :salary
           """)
    List<Employee> findEmployees(
            Double salary);

}
```

---

# Lazy Repository

```java
@Repository
@Lazy
public class EmployeeRepository {

}
```

Created only when needed.

---

# Profile Specific Repository

```java
@Repository
@Profile("dev")
public class MockEmployeeRepository
        implements EmployeeRepository {

}
```

Only active in:

```text
dev
```

profile.

---

# Scope of Repository

Default:

```java
Singleton
```

---

```java
@Repository
@Scope("prototype")
public class EmployeeRepository {

}
```

Rarely used.

---

# Internal Flow

```text
Application Start
        │
        ▼
@ComponentScan
        │
        ▼
Find @Repository
        │
        ▼
Create Bean Definition
        │
        ▼
Create Proxy
        │
        ▼
Enable Exception Translation
        │
        ▼
Store Bean In IoC Container
```

---

# Common Mistakes

## Mistake 1

Putting business logic in repository.

Wrong:

```java
@Repository
public class EmployeeRepository {

    public void calculateSalary() {
    }

}
```

Business logic belongs to:

```java
@Service
```

---

## Mistake 2

Using @Component instead of @Repository.

Works.

But loses semantic clarity and exception translation benefits.

---

## Mistake 3

Calling repository directly from controller.

Wrong:

```java
Controller
   ▼
Repository
```

---

Correct:

```text
Controller
   ▼
Service
   ▼
Repository
```

---

# Common Interview Questions

## What is @Repository?

Marks a class as a DAO/Data Access Layer component.

---

## Is @Repository a Component?

Yes.

Internally it is meta-annotated with:

```java
@Component
```

---

## What is the main advantage over @Component?

Exception Translation.

---

## What exception hierarchy does Spring translate to?

```java
DataAccessException
```

---

## Is @Repository mandatory for JpaRepository?

No.

Spring Data automatically detects repository interfaces.

---

## What layer should use @Repository?

Data Access Layer.

---

## Can @Repository participate in Dependency Injection?

Yes.

Like any Spring Bean.

---

## What is the default scope?

```java
Singleton
```

---

# Real-World Significance

Every enterprise application requires a persistence layer.

Repositories are responsible for:

- Database Communication
- Query Execution
- Data Persistence
- Data Retrieval
- Transaction Participation

Spring's exception translation mechanism significantly reduces database-specific code and improves portability across:

- MySQL
- PostgreSQL
- Oracle
- SQL Server
- DB2

---

# Key Points To Remember

- Specialized form of `@Component`.
- Represents the Data Access Layer.
- Automatically registered as a Spring Bean.
- Supports Dependency Injection.
- Enables Exception Translation.
- Converts vendor-specific exceptions to `DataAccessException`.
- Commonly used with JPA and JDBC.
- Default scope is Singleton.
- Should contain database access logic only.
- Sits between Service Layer and Database.
- One of the most important annotations in Spring Data and Spring Boot.