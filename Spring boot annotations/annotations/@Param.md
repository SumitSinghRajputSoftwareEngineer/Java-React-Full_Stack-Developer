
## Definition

`@Param` is a Spring Data JPA annotation used to bind a **method parameter** to a **named parameter** inside a JPQL or SQL query.

It tells Spring Data JPA:

> Map this Java method parameter to the named parameter used in the query.

Package:

```java
import org.springframework.data.repository.query.Param;
```

---

# Why Do We Need @Param?

Suppose we write:

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.name=:name
""")
```

---

Spring sees:

```java
:name
```

inside the query.

---

Question:

```text
Which method parameter should be used?
```

---

Solution:

```java
@Param("name")
```

---

# Basic Example

Repository:

```java
@Repository
public interface EmployeeRepository
extends JpaRepository<Employee, Long> {

    @Query("""
    SELECT e
    FROM Employee e
    WHERE e.name=:name
    """)
    List<Employee> findByName(
        @Param("name")
        String name
    );
}
```

---

Call:

```java
repository.findByName(
    "Sumit"
);
```

---

Generated SQL:

```sql
SELECT *
FROM employee
WHERE name='Sumit'
```

---

# How Mapping Works

Query:

```java
:name
```

---

Method:

```java
@Param("name")
String name
```

---

Mapping:

```text
:name
   ↓
"Sumit"
```

---

# Multiple Parameters

Very common.

---

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

Call:

```java
findEmployee(
    "Sumit",
    50000.0
);
```

---

Generated:

```sql
SELECT *
FROM employee
WHERE name='Sumit'
AND salary > 50000
```

---

# Without @Param

Possible only if:

```java
Java Parameter Names
```

are retained at runtime.

---

Example:

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.name=:name
""")
List<Employee> findByName(
        String name
);
```

---

May work in newer Spring Boot versions.

---

But production recommendation:

```java
Always use @Param
```

---

# Named Parameters vs Positional Parameters

Interview Favorite.

---

## Positional Parameters

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.name=?1
AND e.salary>?2
""")
```

---

Mapping:

```java
?1 → name
?2 → salary
```

---

Method:

```java
findEmployee(
    String name,
    Double salary
)
```

---

# Named Parameters

```java
:name
:salary
```

---

Method:

```java
@Param("name")
String name

@Param("salary")
Double salary
```

---

Much more readable.

---

Comparison:

| Feature | Positional | Named |
|----------|------------|--------|
| Readability | Poor | Excellent |
| Maintainability | Poor | Excellent |
| Production Usage | Less | More |

---

# Example With LIKE

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.name
LIKE %:name%
""")
List<Employee> search(
    @Param("name")
    String name
);
```

---

Call:

```java
search("mit");
```

---

Returns:

```text
Amit
Sumit
```

---

# Example With IN Clause

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

Generated:

```sql
WHERE id IN (1,2,3)
```

---

# Example With Native Query

```java
@Query(
 value =
 """
 SELECT *
 FROM employee
 WHERE salary > :salary
 """,
 nativeQuery = true
)
List<Employee>
findEmployees(

 @Param("salary")
 Double salary
);
```

---

Works exactly the same.

---

# Example With Update Query

```java
@Transactional

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

Call:

```java
updateSalary(
    1L,
    90000.0
);
```

---

Generated:

```sql
UPDATE employee
SET salary=90000
WHERE id=1
```

---

# Example With Delete Query

```java
@Transactional

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

# Example With Stored Procedure

```java
@Procedure(
 "get_salary"
)
Double getSalary(

 @Param("employee_id")
 Long id
);
```

---

Used for procedure parameter binding.

---

# Internal Workflow

Repository Method:

```java
findByName(
 "Sumit"
)
```

---

Spring Data:

```text
Reads @Query
        ↓
Finds :name
        ↓
Looks For @Param("name")
        ↓
Binds Value
        ↓
Executes Query
```

---

# Real Production Example

Search Employee.

---

Repository:

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.department=:department
AND e.salary>:salary
""")
List<Employee>
findEmployees(

 @Param("department")
 String department,

 @Param("salary")
 Double salary
);
```

---

Call:

```java
findEmployees(
    "IT",
    50000.0
);
```

---

Generated:

```sql
WHERE department='IT'
AND salary>50000
```

---

# Common Mistakes

## Mistake 1

Different Names

Wrong:

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.name=:name
""")
```

---

Method:

```java
@Param("employeeName")
String name
```

---

Query:

```java
:name
```

---

Method:

```java
employeeName
```

---

Mismatch.

---

Error:

```text
Named parameter not bound
```

---

# Mistake 2

Missing @Param

Sometimes:

```java
:name
```

cannot be resolved.

---

Use:

```java
@Param("name")
```

---

# Mistake 3

Typo

Wrong:

```java
:salry
```

---

Method:

```java
@Param("salary")
```

---

Runtime error.

---

# Common Interview Questions

## What is @Param?

Maps repository method parameters to named query parameters.

---

## Why use it?

For named parameter binding.

---

## Is it required?

Not always.

But strongly recommended.

---

## Difference Between Named and Positional Parameters?

Named:

```java
:name
```

---

Positional:

```java
?1
```

---

Named parameters are more readable.

---

## Can It Be Used With Native Queries?

Yes.

---

## Can It Be Used With @Procedure?

Yes.

---

# Enterprise Best Practice

Always prefer:

```java
@Param
```

with:

```java
:name
```

---

Avoid:

```java
?1
?2
?3
```

for complex queries.

---

Use meaningful names:

```java
:department
:salary
:startDate
:endDate
```

---

Improves maintainability.

---

# Key Points To Remember

- `@Param` binds method arguments to named query parameters.
- Used with `@Query`.
- Used with JPQL and Native SQL.
- Can be used with `@Procedure`.
- Improves readability.
- Prevents parameter order mistakes.
- Preferred over positional parameters.
- Commonly used in repository methods.
- Essential Spring Data JPA annotation.
- Frequently asked in interviews.

---

# Quick Comparison

| Syntax | Meaning |
|----------|----------|
| `:name` | Named Parameter |
| `?1` | Positional Parameter |
| `@Param("name")` | Binds Value To `:name` |

---

# Interview Shortcut

```java
@Query("""
SELECT e
FROM Employee e
WHERE e.name=:name
""")
List<Employee> findByName(
    @Param("name")
    String name
);
```

Meaning:

```text
:name In Query
        ↓
Mapped To
        ↓
Method Parameter
        ↓
Value Bound
        ↓
Query Executed
```

Most common production usage:

```java
@Param("id")
Long id

@Param("salary")
Double salary
```

This is the standard Spring Data JPA mechanism for binding Java method parameters to named parameters in JPQL, SQL, and stored procedure calls.