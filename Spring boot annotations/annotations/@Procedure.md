
## Definition

`@Procedure` is a Spring Data JPA annotation used to call a **stored procedure** directly from a repository method.

It tells Spring Data JPA:

> Execute a database stored procedure instead of a normal SQL/JPQL query.

Package:

```java
import org.springframework.data.jpa.repository.query.Procedure;
```

---

# What Is a Stored Procedure?

A Stored Procedure is a precompiled set of SQL statements stored inside the database.

Think of it as:

```text
Java Method
       ↓
Stored Procedure
```

Example:

```sql
CREATE PROCEDURE get_employee_count()
BEGIN
    SELECT COUNT(*)
    FROM employee;
END;
```

---

Database stores it permanently.

---

Applications can call it whenever required.

---

# Why Use Stored Procedures?

Advantages:

```text
Better Performance
Reusable Logic
Reduced Network Calls
Complex Database Operations
Improved Security
```

---

Common Uses:

```text
Payroll Processing
Banking Transactions
Batch Jobs
Financial Calculations
Reporting
```

---

# Why @Procedure?

Without Spring Data JPA:

```java
EntityManager
```

must be used manually.

---

Example:

```java
StoredProcedureQuery query =
    entityManager
        .createStoredProcedureQuery(
            "get_employee_count"
        );
```

---

Verbose.

---

Solution:

```java
@Procedure
```

---

# Basic Example

## Database Procedure

```sql
CREATE PROCEDURE get_employee_count()
BEGIN
    SELECT COUNT(*)
    FROM employee;
END;
```

---

## Repository

```java
@Repository
public interface EmployeeRepository
extends JpaRepository<Employee, Long> {

    @Procedure(
        procedureName =
        "get_employee_count"
    )
    Long getEmployeeCount();
}
```

---

Call:

```java
Long count =
    employeeRepository
        .getEmployeeCount();
```

---

Result:

```text
Total Employee Count
```

---

# Simple Procedure Example

Database:

```sql
CREATE PROCEDURE
get_salary(
 IN emp_id BIGINT
)
BEGIN

 SELECT salary
 FROM employee
 WHERE id=emp_id;

END;
```

---

Repository:

```java
@Procedure(
    procedureName =
    "get_salary"
)
Double getSalary(
        Long empId
);
```

---

Call:

```java
Double salary =
    repository
      .getSalary(1L);
```

---

# Procedure With Input Parameter

Database:

```sql
CREATE PROCEDURE
increase_salary(
 IN emp_id BIGINT,
 IN increment_amt DOUBLE
)
BEGIN

 UPDATE employee
 SET salary =
     salary +
     increment_amt

 WHERE id = emp_id;

END;
```

---

Repository:

```java
@Procedure(
    procedureName =
      "increase_salary"
)
void increaseSalary(
        Long empId,
        Double amount
);
```

---

Call:

```java
repository
    .increaseSalary(
        1L,
        5000.0
    );
```

---

Generated:

```text
Stored Procedure Executed
```

---

# Procedure With Output Parameter

Database:

```sql
CREATE PROCEDURE
employee_count(
 OUT total BIGINT
)
BEGIN

 SELECT COUNT(*)
 INTO total

 FROM employee;

END;
```

---

Repository:

```java
@Procedure(
 procedureName =
 "employee_count"
)
Long employeeCount();
```

---

Result:

```java
Long count =
 repository.employeeCount();
```

---

# Named Stored Procedure

JPA supports:

```java
@NamedStoredProcedureQuery
```

---

Entity:

```java
@Entity

@NamedStoredProcedureQuery(
    name = "Employee.count",

    procedureName =
      "employee_count"
)
public class Employee {
}
```

---

Repository:

```java
@Procedure(
    name =
    "Employee.count"
)
Long employeeCount();
```

---

# Procedure With Parameters

Database:

```sql
CREATE PROCEDURE
find_employee_salary(
 IN employee_id BIGINT
)
BEGIN

 SELECT salary
 FROM employee
 WHERE id=employee_id;

END;
```

---

Repository:

```java
@Procedure(
 procedureName =
 "find_employee_salary"
)
Double findSalary(
 @Param("employee_id")
 Long id
);
```

---

# Procedure Returning Result Set

Database:

```sql
CREATE PROCEDURE
get_all_employees()
BEGIN

 SELECT *
 FROM employee;

END;
```

---

Repository:

```java
@Procedure(
 procedureName =
 "get_all_employees"
)
List<Employee>
getAllEmployees();
```

---

Support depends on:

```text
Database Vendor
Hibernate Version
```

---

# Real Banking Example

Procedure:

```sql
CREATE PROCEDURE
transfer_money(
 IN from_acc BIGINT,
 IN to_acc BIGINT,
 IN amount DOUBLE
)
BEGIN

 UPDATE account
 SET balance =
 balance - amount
 WHERE id=from_acc;

 UPDATE account
 SET balance =
 balance + amount
 WHERE id=to_acc;

END;
```

---

Repository:

```java
@Procedure(
 procedureName =
 "transfer_money"
)
void transferMoney(
        Long from,

        Long to,

        Double amount
);
```

---

Call:

```java
repository.transferMoney(
    1L,
    2L,
    1000.0
);
```

---

Entire transfer handled inside database.

---

# Internal Workflow

Repository Method:

```java
employeeCount()
```

---

Spring Data:

```text
Reads @Procedure
         ↓
Creates CallableStatement
         ↓
Executes Stored Procedure
         ↓
Maps Result
         ↓
Returns Output
```

---

# @Procedure vs @Query

Interview Favorite.

---

## @Query

Used for:

```text
JPQL
Native SQL
```

---

Example:

```java
@Query(
 "SELECT e
  FROM Employee e"
)
```

---

## @Procedure

Used for:

```text
Stored Procedures
```

---

Example:

```java
@Procedure(
 "employee_count"
)
```

---

Comparison:

| Feature | @Query | @Procedure |
|----------|---------|------------|
| JPQL | ✅ | ❌ |
| Native SQL | ✅ | ❌ |
| Stored Procedure | ❌ | ✅ |
| Business Logic In DB | ❌ | ✅ |
| Complex DB Processing | Limited | Excellent |

---

# @Procedure vs EntityManager

Without @Procedure:

```java
StoredProcedureQuery query =
entityManager
.createStoredProcedureQuery(
    "employee_count"
);
```

---

Lots of boilerplate.

---

With:

```java
@Procedure
```

---

Single method.

---

Cleaner.

---

# Common Mistakes

## Mistake 1

Procedure Doesn't Exist

Wrong:

```java
@Procedure(
 "abc"
)
```

---

Database:

```text
Procedure Not Found
```

---

Runtime Error.

---

# Mistake 2

Parameter Mismatch

Procedure:

```sql
IN emp_id
```

---

Method:

```java
Long id
```

without mapping.

---

May fail.

---

Use:

```java
@Param
```

---

# Mistake 3

Expecting Entity Lifecycle Events

Stored procedure:

```sql
UPDATE employee
```

---

Does NOT trigger:

```java
@PreUpdate
@PostUpdate
```

---

Because JPA is bypassed.

---

# Common Interview Questions

## What is @Procedure?

Used to execute database stored procedures.

---

## Why use Stored Procedures?

```text
Performance
Reusable DB Logic
Complex Transactions
```

---

## Difference Between @Procedure and @Query?

`@Query`

```text
JPQL/SQL
```

---

`@Procedure`

```text
Stored Procedure
```

---

## Does @Procedure Use EntityManager Internally?

Yes.

Spring Data creates and executes a `StoredProcedureQuery`.

---

## Can It Return Values?

Yes.

Supports:

```text
IN
OUT
INOUT
Result Sets
```

depending on database support.

---

# Enterprise Best Practice

Use:

```java
@Query
```

for:

```text
Normal CRUD Queries
```

---

Use:

```java
@Procedure
```

for:

```text
Complex Database Logic
Bulk Operations
Financial Calculations
Reporting
Batch Processing
```

---

Keep business logic in Java unless:

```text
Performance
Legacy Systems
DB-Centric Architecture
```

require stored procedures.

---

# Key Points To Remember

- `@Procedure` calls database stored procedures.
- Used inside Spring Data JPA repositories.
- Supports IN, OUT, and INOUT parameters.
- Supports procedure names and named stored procedures.
- Internally uses `StoredProcedureQuery`.
- Useful for database-heavy processing.
- Often used in banking, finance, ERP, and reporting systems.
- Bypasses normal JPQL query execution.
- Can return scalar values or result sets.
- Important Spring Data JPA interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Query` | JPQL / Native SQL |
| `@Modifying` | Update/Delete |
| `@Procedure` | Stored Procedure |
| `@Transactional` | Transaction Management |
| `@Param` | Parameter Mapping |

---

# Interview Shortcut

```java
@Procedure(
    procedureName =
    "employee_count"
)
Long employeeCount();
```

Meaning:

```text
Repository Method Called
          ↓
Spring Creates CallableStatement
          ↓
Database Stored Procedure Executes
          ↓
Result Returned
```

Typical production usage:

```java
@Procedure(
 procedureName =
 "transfer_money"
)
void transferMoney(
    Long from,
    Long to,
    Double amount
);
```

This is the standard Spring Data JPA mechanism for invoking database stored procedures directly from repository interfaces.