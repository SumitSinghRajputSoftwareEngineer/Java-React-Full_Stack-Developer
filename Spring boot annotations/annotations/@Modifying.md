
## Definition

`@Modifying` is a Spring Data JPA annotation used to indicate that a repository query **modifies data** instead of just reading data.

It tells Spring Data JPA:

> This query performs an UPDATE, DELETE, INSERT, or DDL operation and should be executed as a modifying query.

Package:

```java
import org.springframework.data.jpa.repository.Modifying;
```

---

# Why Do We Need @Modifying?

Normally, Spring Data JPA assumes:

```java
@Query(...)
```

means:

```text
SELECT Query
```

---

Example:

```java
@Query(
 "SELECT e FROM Employee e"
)
```

returns data.

---

But for:

```sql
UPDATE
DELETE
INSERT
```

Spring needs additional information.

---

Solution:

```java
@Modifying
```

---

# Basic Example

## Update Query

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

Without:

```java
@Modifying
```

---

Spring throws:

```text
InvalidDataAccessApiUsageException
```

---

# What Does It Do Internally?

Normal Query:

```java
SELECT
```

uses:

```java
getResultList()
```

or

```java
getSingleResult()
```

---

Modifying Query:

```java
UPDATE
DELETE
```

uses:

```java
executeUpdate()
```

---

`@Modifying` tells Spring which execution strategy to use.

---

# Update Example

Repository:

```java
@Repository
public interface EmployeeRepository
extends JpaRepository<Employee, Long> {

    @Modifying

    @Query("""
    UPDATE Employee e
    SET e.salary=:salary
    WHERE e.id=:id
    """)
    int updateSalary(
            Long id,
            Double salary);
}
```

---

Service:

```java
employeeRepository
    .updateSalary(
        1L,
        90000.0
    );
```

---

Generated SQL:

```sql
UPDATE employee
SET salary=90000
WHERE id=1
```

---

Return Value:

```java
1
```

Meaning:

```text
1 row updated
```

---

# Delete Example

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

Generated:

```sql
DELETE
FROM employee
WHERE id=?
```

---

Return:

```java
1
```

---

Meaning:

```text
1 row deleted
```

---

# Transaction Requirement

Very Important.

---

For modifying queries:

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

@Query("""
DELETE
FROM Employee e
WHERE e.id=:id
""")
int deleteEmployee(
        Long id
);
```

---

Without transaction:

```text
TransactionRequiredException
```

may occur.

---

# Insert Query Example

Rare but possible.

---

```java
@Transactional

@Modifying

@Query(
 value =
 """
 INSERT INTO employee
 (
  id,
  name
 )
 VALUES
 (
  :id,
  :name
 )
 """,
 nativeQuery = true
)
int insertEmployee(
        Long id,
        String name
);
```

---

Usually:

```java
save()
```

is preferred.

---

# Bulk Update Example

Increase salary.

---

```java
@Transactional

@Modifying

@Query("""
UPDATE Employee e
SET e.salary =
    e.salary + 5000
""")
int incrementSalary();
```

---

Generated:

```sql
UPDATE employee
SET salary=salary+5000
```

---

Return:

```java
Number of affected rows
```

---

# Bulk Delete Example

Delete inactive employees.

---

```java
@Transactional

@Modifying

@Query("""
DELETE
FROM Employee e
WHERE e.active=false
""")
int deleteInactive();
```

---

# clearAutomatically

Important Advanced Feature.

---

Example:

```java
@Modifying(
    clearAutomatically = true
)
```

---

Why?

Because:

```text
Persistence Context
```

may contain stale data after update.

---

Example:

```java
@Modifying(
    clearAutomatically = true
)
```

---

Spring clears cache automatically.

---

# flushAutomatically

Another Advanced Feature.

---

```java
@Modifying(
    flushAutomatically = true
)
```

---

Before executing query:

```java
EntityManager.flush()
```

is called.

---

Ensures pending changes are written first.

---

# Full Example

```java
@Transactional

@Modifying(
    clearAutomatically = true,
    flushAutomatically = true
)

@Query("""
UPDATE Employee e
SET e.salary=:salary
WHERE e.id=:id
""")
int updateSalary(
        Long id,
        Double salary
);
```

---

# Internal Workflow

Repository Method:

```java
updateSalary(...)
```

---

Spring Data:

```text
Read @Query
        ↓
Read @Modifying
        ↓
Create Update Query
        ↓
Execute executeUpdate()
        ↓
Return Updated Rows
```

---

# @Modifying vs save()

Interview Favorite.

---

## save()

```java
employee.setSalary(50000);

repository.save(employee);
```

---

Entity managed.

---

Triggers:

```text
Dirty Checking
Lifecycle Events
Auditing
```

---

## @Modifying

```java
UPDATE Employee e
SET e.salary=50000
```

---

Direct database update.

---

Faster for bulk operations.

---

Comparison:

| Feature | save() | @Modifying |
|----------|--------|------------|
| Single Record | ✅ | ✅ |
| Bulk Update | ❌ | ✅ |
| Dirty Checking | ✅ | ❌ |
| Lifecycle Events | ✅ | ❌ |
| Performance | Normal | Better |

---

# Important Limitation

Bulk update:

```java
@Modifying
```

---

Does NOT trigger:

```java
@PreUpdate
@PostUpdate
```

---

Because entities are not loaded.

---

Example:

```java
UPDATE Employee
SET salary=1000
```

---

Direct DB update.

---

No entity lifecycle callbacks.

---

# Common Mistakes

## Mistake 1

Missing @Modifying

Wrong:

```java
@Query("""
UPDATE Employee e
SET e.salary=5000
""")
```

---

Spring expects:

```text
SELECT
```

---

Fails.

---

# Mistake 2

Missing Transaction

Wrong:

```java
@Modifying
@Query(...)
```

---

Need:

```java
@Transactional
```

---

# Mistake 3

Expecting Entity List

Wrong:

```java
List<Employee>
```

for update query.

---

Correct:

```java
int
```

or

```java
void
```

---

# Common Interview Questions

## What is @Modifying?

Marks a query as INSERT, UPDATE, DELETE, or DDL operation.

---

## Is it required for SELECT?

No.

Only modifying queries.

---

## Why is it used?

To switch execution from:

```java
getResultList()
```

to:

```java
executeUpdate()
```

---

## Does it require Transaction?

Usually yes.

---

## Return Value?

Typically:

```java
int
```

representing affected rows.

---

## Does it trigger lifecycle callbacks?

No.

Bulk updates bypass entity lifecycle events.

---

# Enterprise Best Practice

Use:

```java
save()
```

for:

```text
Single Entity Updates
```

---

Use:

```java
@Modifying
```

for:

```text
Bulk Updates
Bulk Deletes
Performance-Critical Operations
```

---

Prefer:

```java
clearAutomatically=true
```

for large updates.

---

# Key Points To Remember

- `@Modifying` marks UPDATE, DELETE, INSERT, or DDL queries.
- Used together with `@Query`.
- Usually requires `@Transactional`.
- Executes `executeUpdate()` internally.
- Returns affected row count.
- Supports bulk operations efficiently.
- Can use `clearAutomatically` and `flushAutomatically`.
- Does not trigger entity lifecycle callbacks.
- Bypasses dirty checking.
- Important Spring Data JPA interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Query` | Custom Query |
| `@Modifying` | Update/Delete Query |
| `@Transactional` | Transaction Management |
| `@Param` | Named Parameters |
| `@EntityGraph` | Fetch Optimization |

---

# Interview Shortcut

```java
@Transactional

@Modifying

@Query("""
UPDATE Employee e
SET e.salary=:salary
WHERE e.id=:id
""")
```

Meaning:

```text
Custom Query
       ↓
Modifies Database
       ↓
executeUpdate()
       ↓
Returns Affected Rows
```

Typical production usage:

```java
@Modifying
@Query("""
DELETE
FROM Employee e
WHERE e.active=false
""")
```

This is the standard Spring Data JPA mechanism for executing bulk UPDATE and DELETE operations efficiently without loading entities into memory.