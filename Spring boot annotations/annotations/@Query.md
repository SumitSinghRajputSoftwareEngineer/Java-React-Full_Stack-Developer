
## Definition

`@Query` is a Spring Data JPA annotation used to define **custom JPQL or Native SQL queries** directly inside repository interfaces.

It tells Spring Data JPA:

> Execute this query instead of generating one automatically from the method name.

Package:

```java
import org.springframework.data.jpa.repository.Query;
```

---

# Why Do We Need @Query?

Spring Data JPA can generate queries automatically:

```java
findByName(String name)
```

---

Generates:

```sql
SELECT *
FROM employee
WHERE name = ?
```

---

But sometimes queries become complex:

```text
JOIN Queries
Aggregation Queries
Subqueries
Custom Projections
Native SQL
```

---

Method names become ugly:

```java
findByDepartmentNameAndSalaryGreaterThanAndAgeLessThan(...)
```

---

Solution:

```java
@Query
```

---

# Basic Syntax

```java
@Query("JPQL Query")
```

Example:

```java
@Query(
    "SELECT e FROM Employee e"
)
List<Employee> findAllEmployees();
```

---

# JPQL vs SQL

Very Important Interview Question.

---

## JPQL

Uses:

```text
Entity Name
Field Name
```

---

Example:

```java
@Query(
 "SELECT e FROM Employee e"
)
```

---

NOT:

```sql
SELECT *
FROM employee
```

---

## Native SQL

Uses:

```text
Table Name
Column Name
```

---

Example:

```java
@Query(
 value =
   "SELECT * FROM employee",
 nativeQuery = true
)
```

---

# Simple Example

Entity:

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String name;
}
```

---

Repository:

```java
@Repository
public interface EmployeeRepository
extends JpaRepository<Employee, Long> {

    @Query(
      "SELECT e FROM Employee e"
    )
    List<Employee> getEmployees();
}
```

---

Generated:

```sql
SELECT *
FROM employee
```

---

# Query With WHERE Clause

```java
@Query(
 "SELECT e FROM Employee e
  WHERE e.name = ?1"
)
List<Employee>
findByName(String name);
```

---

Call:

```java
findByName("Sumit");
```

---

Generated:

```sql
SELECT *
FROM employee
WHERE name='Sumit'
```

---

# Positional Parameters

Use:

```java
?1
?2
?3
```

---

Example:

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.name=?1
AND e.salary>?2
""")
List<Employee> findEmployee(
        String name,
        Double salary);
```

---

Meaning:

```java
?1 -> name
?2 -> salary
```

---

# Named Parameters

Preferred in Production.

---

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.name=:name
""")
```

---

Repository:

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.name=:name
""")
List<Employee>
findByName(
    @Param("name")
    String name
);
```

---

Call:

```java
findByName("Sumit");
```

---

Generated:

```sql
SELECT *
FROM employee
WHERE name='Sumit'
```

---

# Multiple Parameters

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.name=:name
AND e.salary>:salary
""")
List<Employee> findEmployee(
    @Param("name")
    String name,

    @Param("salary")
    Double salary
);
```

---

# LIKE Query

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.name
LIKE %:name%
""")
```

---

Example:

```java
findEmployee("mit")
```

---

Returns:

```text
Sumit
Amit
```

---

# IN Clause

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.id IN :ids
""")
List<Employee> findByIds(
    @Param("ids")
    List<Long> ids
);
```

---

Call:

```java
findByIds(
 List.of(1L,2L,3L)
);
```

---

# ORDER BY

```java
@Query("""
SELECT e
FROM Employee e
ORDER BY e.salary DESC
""")
```

---

Generated:

```sql
ORDER BY salary DESC
```

---

# Aggregation Functions

## COUNT

```java
@Query("""
SELECT COUNT(e)
FROM Employee e
""")
Long countEmployees();
```

---

## MAX

```java
@Query("""
SELECT MAX(e.salary)
FROM Employee e
""")
Double maxSalary();
```

---

## MIN

```java
@Query("""
SELECT MIN(e.salary)
FROM Employee e
""")
Double minSalary();
```

---

## AVG

```java
@Query("""
SELECT AVG(e.salary)
FROM Employee e
""")
Double avgSalary();
```

---

## SUM

```java
@Query("""
SELECT SUM(e.salary)
FROM Employee e
""")
Double totalSalary();
```

---

# JOIN Query

Entities:

```java
Employee
    ↓
Department
```

---

Repository:

```java
@Query("""
SELECT e
FROM Employee e
JOIN e.department d
WHERE d.name=:dept
""")
List<Employee> findByDept(
    @Param("dept")
    String dept
);
```

---

Generated:

```sql
JOIN department
```

---

# FETCH JOIN

Avoid N+1 problem.

---

```java
@Query("""
SELECT e
FROM Employee e
JOIN FETCH e.department
""")
```

---

Loads:

```text
Employee
Department
```

in one query.

---

# Projection Query

Return specific columns.

---

DTO:

```java
public record EmployeeDTO(
        String name,
        Double salary
) {}
```

---

Query:

```java
@Query("""
SELECT new
com.demo.EmployeeDTO(
 e.name,
 e.salary
)
FROM Employee e
""")
```

---

Only required fields fetched.

---

# Native Query

Use actual SQL.

---

```java
@Query(
 value =
   "SELECT * FROM employee",
 nativeQuery = true
)
List<Employee> getAll();
```

---

Native Query Example

```java
@Query(
 value =
 """
 SELECT *
 FROM employee
 WHERE salary > ?
 """,

 nativeQuery = true
)
List<Employee>
findHighSalary(
    Double salary
);
```

---

# Update Query

Need:

```java
@Modifying
```

---

Example:

```java
@Modifying

@Query("""
UPDATE Employee e
SET e.salary=:salary
WHERE e.id=:id
""")
int updateSalary(
    @Param("id")
    Long id,

    @Param("salary")
    Double salary
);
```

---

Returns:

```text
Rows Updated
```

---

# Delete Query

```java
@Modifying

@Query("""
DELETE
FROM Employee e
WHERE e.id=:id
""")
int deleteEmployee(
    @Param("id")
    Long id
);
```

---

# Transaction Required

For:

```java
UPDATE
DELETE
```

Need:

```java
@Transactional
```

---

Example:

```java
@Transactional

@Modifying
@Query(...)
```

---

# Internal Workflow

Repository Call:

```java
findByName("Sumit");
```

---

Spring Data:

```text
Reads @Query
        ↓
Creates JPQL/SQL
        ↓
Hibernate Executes
        ↓
Maps Result
        ↓
Returns Entity
```

---

# @Query vs Method Naming

Method Naming:

```java
findByNameAndSalaryGreaterThan(...)
```

---

Good for simple queries.

---

@Query:

```java
@Query(...)
```

---

Good for:

```text
Complex Queries
Joins
Aggregations
Projections
Native SQL
```

---

Comparison:

| Feature | Method Name | @Query |
|----------|------------|---------|
| Simple Query | ✅ | ✅ |
| Complex Query | ❌ | ✅ |
| Join | Limited | ✅ |
| Native SQL | ❌ | ✅ |
| Projection | Limited | ✅ |

---

# Common Mistakes

## Mistake 1

Using Table Name In JPQL

Wrong:

```java
@Query(
 "SELECT * FROM employee"
)
```

---

JPQL uses:

```java
Employee
```

not:

```sql
employee
```

---

Correct:

```java
@Query(
 "SELECT e FROM Employee e"
)
```

---

# Mistake 2

Forgetting @Modifying

Wrong:

```java
@Query(
 "UPDATE Employee e ..."
)
```

---

Need:

```java
@Modifying
```

---

# Mistake 3

Forgetting Transaction

For:

```java
UPDATE
DELETE
```

Need:

```java
@Transactional
```

---

# Common Interview Questions

## What is @Query?

Used to define custom JPQL or SQL queries.

---

## Difference Between JPQL and SQL?

JPQL:

```text
Entity Names
Field Names
```

SQL:

```text
Table Names
Column Names
```

---

## How To Use Native SQL?

```java
@Query(
 value="...",
 nativeQuery=true
)
```

---

## How To Update Data?

```java
@Modifying
@Query(...)
```

---

## Why Use Named Parameters?

Better readability.

---

# Enterprise Best Practice

Use:

```java
Method Names
```

for simple queries.

---

Use:

```java
@Query
```

for:

```text
Joins
Aggregations
Projections
Complex Conditions
Native SQL
```

---

Prefer:

```java
Named Parameters
```

instead of:

```java
?1 ?2 ?3
```

---

# Key Points To Remember

- `@Query` defines custom JPQL or SQL queries.
- Works inside repository interfaces.
- Supports JPQL and Native SQL.
- Supports named and positional parameters.
- Supports joins, aggregations, projections, and subqueries.
- Update/Delete queries require `@Modifying`.
- Update/Delete queries usually require `@Transactional`.
- Preferred for complex database operations.
- Very important Spring Data JPA interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Query` | Custom Query |
| `@Param` | Named Parameter |
| `@Modifying` | Update/Delete Query |
| `@Transactional` | Transaction Management |
| `@EntityGraph` | Fetch Optimization |

---

# Interview Shortcut

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.name=:name
""")
```

Meaning:

```text
Skip Auto Query Generation
          ↓
Execute Custom JPQL
          ↓
Map Results To Entity
          ↓
Return Data
```

Most common production usage:

```java
@Query("""
SELECT e
FROM Employee e
JOIN e.department d
WHERE d.name=:dept
""")
```

This is the standard Spring Data JPA mechanism for writing custom database queries when method-name-based query generation is insufficient.