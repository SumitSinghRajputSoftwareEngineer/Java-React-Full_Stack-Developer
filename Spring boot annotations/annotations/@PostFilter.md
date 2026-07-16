
## Definition

`@PostFilter` is a Spring Security annotation used to **filter a collection after a method executes and before the result is returned to the caller**.

It tells Spring:

> Execute the method, inspect the returned collection, remove unauthorized elements, and then return the filtered result.

Package:

```java
import org.springframework.security.access.prepost.PostFilter;
```

---

# Why Do We Need @PostFilter?

Suppose a method returns:

```java
[
 Employee(owner="sumit"),
 Employee(owner="admin"),
 Employee(owner="sumit")
]
```

You want:

```text
Users should only see
their own employees.
```

Without `@PostFilter`:

```java
List<Employee> employees =
        repository.findAll();

return employees.stream()
       .filter(...)
       .toList();
```

---

Need manual filtering everywhere.

---

Solution:

```java
@PostFilter
```

---

# Prerequisite

Method security must be enabled.

```java
@Configuration

@EnableMethodSecurity
public class SecurityConfig {
}
```

---

Without:

```java
@EnableMethodSecurity
```

the annotation will not work.

---

# What Happens Internally?

Method Call:

```java
employeeService.getEmployees();
```

---

Flow:

```text
Execute Method
       ↓
Get Collection
       ↓
Filter Elements
       ↓
Remove Unauthorized Records
       ↓
Return Result
```

---

# Basic Example

```java
@PostFilter(
 "filterObject.active"
)
public List<Employee>
getEmployees() {

    return repository.findAll();
}
```

---

Method Returns:

```java
[
 active=true,
 active=false,
 active=true
]
```

---

After Filtering:

```java
[
 active=true,
 active=true
]
```

---

Returned to caller:

```text
Only Active Employees
```

---

# Special Variable: filterObject

Inside:

```java
@PostFilter
```

Spring exposes:

```java
filterObject
```

---

It represents:

```text
Current Element
In Returned Collection
```

---

Example:

```java
@PostFilter(
 "filterObject.active"
)
```

Equivalent to:

```java
employee.isActive()
```

---

# Ownership Example

Employee:

```java
public class Employee {

    private String owner;
}
```

---

Method:

```java
@PostFilter(
 "filterObject.owner ==
  authentication.name"
)
public List<Employee>
getEmployees() {

    return repository.findAll();
}
```

---

Logged In User:

```text
sumit
```

---

Returned Data:

```java
[
 owner=sumit,
 owner=admin,
 owner=sumit
]
```

---

After Filtering:

```java
[
 owner=sumit,
 owner=sumit
]
```

---

Only authorized records are returned.

---

# Complete Example

```java
@Service
public class EmployeeService {

    @PostFilter(
      "filterObject.owner ==
       authentication.name"
    )
    public List<Employee>
    getEmployees() {

        return repository.findAll();
    }
}
```

---

# Filtering By Role

```java
@PostFilter(
 "hasRole('ADMIN')
  or
  filterObject.owner ==
  authentication.name"
)
```

---

Meaning:

```text
ADMIN Sees Everything
Users See Their Own Records
```

---

# Filtering Numeric Collections

Example:

```java
@PostFilter(
 "filterObject > 100"
)
public List<Integer>
getNumbers() {

    return List.of(
      10, 50, 150, 200
    );
}
```

---

Returned:

```java
[
 150,
 200
]
```

---

# Filtering Documents

```java
@PostFilter(
 "filterObject.createdBy ==
  authentication.name"
)
public List<Document>
getDocuments() {

    return repository.findAll();
}
```

---

Only user-owned documents are returned.

---

# Supported Return Types

Works with:

```java
Collection
List
Set
Array
Map
```

---

Examples:

```java
List<Employee>
Set<Employee>
Employee[]
Map<Long, Employee>
```

---

# Internal Workflow

Returned Collection:

```java
List<Employee>
```

---

Flow:

```text
Method Executes
       ↓
Collection Returned
       ↓
Evaluate Each Element
       ↓
Keep Matching Elements
       ↓
Remove Others
       ↓
Return Filtered Collection
```

---

# @PostFilter vs @PreFilter

Interview Favorite.

---

## @PreFilter

Filters:

```text
Input Collection
```

---

Before method execution.

---

Example:

```java
processEmployees(
   employees
)
```

---

Input gets filtered.

---

# @PostFilter

Filters:

```text
Returned Collection
```

---

After method execution.

---

Example:

```java
getEmployees()
```

---

Output gets filtered.

---

Comparison:

| Feature | @PreFilter | @PostFilter |
|----------|-----------|-------------|
| Input Collection | ✅ | ❌ |
| Output Collection | ❌ | ✅ |
| Before Method | ✅ | ❌ |
| After Method | ❌ | ✅ |

---

# @PostFilter vs @PostAuthorize

Interview Favorite.

---

## @PostAuthorize

Works on:

```java
Single Object
```

---

Example:

```java
Employee getEmployee()
```

---

Uses:

```java
returnObject
```

---

# @PostFilter

Works on:

```java
Collections
```

---

Uses:

```java
filterObject
```

---

Comparison:

| Feature | @PostAuthorize | @PostFilter |
|----------|---------------|------------|
| Single Object | ✅ | ❌ |
| Collection | ❌ | ✅ |
| returnObject | ✅ | ❌ |
| filterObject | ❌ | ✅ |

---

# Example Using Map

```java
@PostFilter(
 "filterObject.value.owner ==
  authentication.name"
)
public Map<Long, Employee>
getEmployees() {

    return employeeMap;
}
```

---

Spring evaluates:

```java
Map.Entry
```

elements.

---

# Real-World Example

Document Management System.

Method:

```java
List<Document>
getDocuments()
```

---

Returns:

```text
All Documents
```

---

Security:

```java
@PostFilter(
 "filterObject.owner ==
  authentication.name"
)
```

---

User receives:

```text
Only Own Documents
```

---

# Common Mistakes

## Mistake 1

Using On Single Object

Wrong:

```java
Employee getEmployee()
```

---

Use:

```java
@PostAuthorize
```

instead.

---

# Mistake 2

Large Collections

Method:

```java
return 1 Million Records;
```

---

Spring filters after retrieval.

---

Can hurt performance.

---

Better:

```text
Filter In Database
```

when possible.

---

# Mistake 3

Forgetting EnableMethodSecurity

Without:

```java
@EnableMethodSecurity
```

annotation won't work.

---

# Performance Consideration

Method:

```java
repository.findAll()
```

returns:

```text
100,000 Records
```

---

Then:

```java
@PostFilter
```

filters them.

---

This means:

```text
Database Load
Memory Usage
CPU Usage
```

can increase.

---

For huge datasets:

```text
Filter In Query
```

instead.

---

# Common Interview Questions

## What is @PostFilter?

Filters returned collections after method execution.

---

## Which Special Variable Is Used?

```java
filterObject
```

---

## Can It Filter Single Objects?

No.

Use:

```java
@PostAuthorize
```

---

## Difference Between @PreFilter and @PostFilter?

### @PreFilter

Filters input.

---

### @PostFilter

Filters output.

---

## Difference Between @PostAuthorize and @PostFilter?

### @PostAuthorize

Single object.

---

### @PostFilter

Collection.

---

# Enterprise Best Practice

Use:

```java
@PostFilter
```

for:

```text
Ownership-Based Filtering
Permission-Based Filtering
Document Security
Employee Visibility
Customer Visibility
```

---

Avoid for:

```text
Very Large Collections
```

---

Prefer database filtering when scalability matters.

---

# Key Points To Remember

- `@PostFilter` filters returned collections after method execution.
- Requires `@EnableMethodSecurity`.
- Uses Spring Expression Language (SpEL).
- Provides `filterObject`.
- Works on collections, arrays, sets, and maps.
- Commonly used for ownership-based visibility.
- Uses Spring AOP proxies internally.
- Executes after method completion.
- Different from `@PostAuthorize`.
- Frequently asked Spring Security interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@PreAuthorize` | Check Access Before Method |
| `@PostAuthorize` | Check Access After Method |
| `@PreFilter` | Filter Input Collection |
| `@PostFilter` | Filter Output Collection |
| `@EnableMethodSecurity` | Enable Method Security |

---

# Interview Shortcut

```java
@PostFilter(
 "filterObject.owner ==
  authentication.name"
)
public List<Employee>
getEmployees() {

    return repository.findAll();
}
```

Meaning:

```text
Method Executes
       ↓
Collection Returned
       ↓
Evaluate Each Element
       ↓
Keep Authorized Elements
       ↓
Remove Unauthorized Elements
       ↓
Return Filtered Collection
```

Think of it as:

```java
employees.stream()
         .filter(
            e -> e.getOwner()
                  .equals(currentUser)
         )
         .toList();
```

executed automatically by Spring Security after the method finishes.

`@PostFilter` is used when you want Spring Security to automatically remove unauthorized elements from returned collections before they are sent back to the caller.