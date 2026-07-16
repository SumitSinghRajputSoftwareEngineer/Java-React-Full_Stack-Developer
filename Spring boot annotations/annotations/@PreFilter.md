
## Definition

`@PreFilter` is a Spring Security annotation used to **filter collections or arrays before a method executes**.

It tells Spring:

> Remove elements from the input collection that do not satisfy the specified security expression before calling the method.

Package:

```java
import org.springframework.security.access.prepost.PreFilter;
```

---

# Why Do We Need @PreFilter?

Suppose a user submits a list of employees:

```java
[
  Employee(1),
  Employee(2),
  Employee(3)
]
```

You want:

```text
Process Only Active Employees
```

or

```text
Process Only Records
Owned By Current User
```

---

Without `@PreFilter`:

```java
for(Employee e : employees) {

    if(checkPermission(e)) {

        process(e);
    }
}
```

---

Need manual filtering.

---

Solution:

```java
@PreFilter
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

# What Happens Internally?

Method Call:

```java
employeeService.processEmployees(
      employees
);
```

---

Flow:

```text
Collection Received
       ↓
Filter Elements
       ↓
Remove Unauthorized Items
       ↓
Execute Method
```

---

# Basic Example

```java
@PreFilter(
 "filterObject.active"
)
public void processEmployees(
        List<Employee> employees
) {

    System.out.println(
       employees.size()
    );
}
```

---

Input:

```java
[
 Employee(active=true),
 Employee(active=false),
 Employee(active=true)
]
```

---

After Filtering:

```java
[
 Employee(active=true),
 Employee(active=true)
]
```

---

Method receives:

```text
2 Employees
```

---

# Special Variable: filterObject

Inside `@PreFilter`, Spring provides:

```java
filterObject
```

---

It represents:

```text
Current Element
Being Evaluated
```

---

Example:

```java
@PreFilter(
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
@PreFilter(
 "filterObject.owner ==
  authentication.name"
)
public void process(
       List<Employee> employees
) {
}
```

---

Logged In User:

```text
sumit
```

---

Input:

```java
[
 owner=sumit,
 owner=admin,
 owner=sumit
]
```

---

Filtered Result:

```java
[
 owner=sumit,
 owner=sumit
]
```

---

Method receives only:

```text
Authorized Records
```

---

# Complete Example

```java
@Service
public class EmployeeService {

    @PreFilter(
      "filterObject.owner ==
       authentication.name"
    )
    public void processEmployees(
         List<Employee> employees
    ) {

        employees.forEach(
          System.out::println
        );
    }
}
```

---

# Filtering By Role

```java
@PreFilter(
 "hasRole('ADMIN')
  or
  filterObject.owner ==
  authentication.name"
)
```

---

Meaning:

```text
ADMIN Can Process All
Users Process Their Own
```

---

# Filtering Arrays

Works with arrays.

---

```java
@PreFilter(
 "filterObject > 10"
)
public void process(
       Integer[] numbers
) {
}
```

---

Input:

```java
[5, 10, 15, 20]
```

---

After Filtering:

```java
[15, 20]
```

---

# Filtering Sets

Works with:

```java
Set
```

---

Example:

```java
@PreFilter(
 "filterObject.active"
)
public void process(
       Set<Employee> employees
) {
}
```

---

# Filtering Multiple Parameters

Suppose:

```java
public void process(
       List<Employee> employees,
       String department
)
```

---

Which parameter should be filtered?

---

Specify:

```java
@PreFilter(
   value =
   "filterObject.active",

   filterTarget =
   "employees"
)
```

---

Meaning:

```text
Filter Only employees Parameter
```

---

# filterTarget Attribute

Syntax:

```java
@PreFilter(
   value =
   "filterObject.active",

   filterTarget =
   "employees"
)
```

---

Without:

```java
filterTarget
```

Spring expects:

```text
Single Collection Parameter
```

---

# Internal Workflow

Input:

```java
List<Employee>
```

---

Flow:

```text
Method Call
      ↓
Evaluate Each Element
      ↓
Keep Matching Elements
      ↓
Discard Others
      ↓
Execute Method
```

---

# Example Using IDs

```java
@PreFilter(
 "filterObject > 100"
)
public void process(
      List<Long> ids
) {
}
```

---

Input:

```java
[10, 50, 150, 200]
```

---

Result:

```java
[150, 200]
```

---

# @PreFilter vs @PostFilter

Interview Favorite.

---

## @PreFilter

Filters:

```text
Method Input
```

---

Before execution.

---

Example:

```java
List<Employee>
```

input filtered.

---

# @PostFilter

Filters:

```text
Method Output
```

---

After execution.

---

Example:

```java
List<Employee>
```

returned list filtered.

---

Comparison:

| Feature | @PreFilter | @PostFilter |
|----------|-----------|-------------|
| Input Collection | ✅ | ❌ |
| Output Collection | ❌ | ✅ |
| Before Method | ✅ | ❌ |
| After Method | ❌ | ✅ |

---

# Real-World Example

Document System:

```java
processDocuments(
   List<Document>
)
```

---

Security:

```java
@PreFilter(
 "filterObject.owner ==
  authentication.name"
)
```

---

Only documents owned by the user reach the method.

---

# Common Mistakes

## Mistake 1

Using On Non-Collection Parameter

Wrong:

```java
public void process(
       Employee employee
)
```

---

`@PreFilter`

requires:

```text
Collection
Array
Map
Set
```

---

# Mistake 2

Forgetting filterTarget

Multiple collections:

```java
process(
  employees,
  departments
)
```

---

Need:

```java
filterTarget="employees"
```

---

# Mistake 3

Forgetting EnableMethodSecurity

Without:

```java
@EnableMethodSecurity
```

annotation won't work.

---

# Supported Types

`@PreFilter` supports:

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

# Common Interview Questions

## What is @PreFilter?

Filters collection arguments before method execution.

---

## Which Special Variable Is Used?

```java
filterObject
```

---

## Does It Work On Single Objects?

No.

Only collections and arrays.

---

## Difference Between @PreAuthorize and @PreFilter?

### @PreAuthorize

Checks method access.

---

### @PreFilter

Filters collection elements.

---

## Difference Between @PreFilter and @PostFilter?

### @PreFilter

Filters input.

---

### @PostFilter

Filters output.

---

# Enterprise Best Practice

Use:

```java
@PreFilter
```

for:

```text
Bulk Operations
Document Processing
Employee Processing
Ownership-Based Filtering
Permission-Based Filtering
```

---

Examples:

```java
Batch Updates
Bulk Deletes
Mass Notifications
File Processing
```

---

Avoid manual filtering when Spring Security can handle it declaratively.

---

# Key Points To Remember

- `@PreFilter` filters collection arguments before method execution.
- Requires `@EnableMethodSecurity`.
- Uses Spring Expression Language (SpEL).
- Provides `filterObject`.
- Works with collections, arrays, sets, and maps.
- Supports ownership and permission filtering.
- Uses Spring AOP proxies internally.
- Executes before the method runs.
- Commonly paired with `@PostFilter`.
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
@PreFilter(
 "filterObject.owner ==
  authentication.name"
)
public void processEmployees(
     List<Employee> employees
) {
}
```

Meaning:

```text
Input Collection
        ↓
Evaluate Each Element
        ↓
Keep Authorized Elements
        ↓
Remove Unauthorized Elements
        ↓
Execute Method
```

Think of it as:

```java
employees.removeIf(
   e -> !e.getOwner()
          .equals(currentUser)
);
```

executed automatically by Spring Security before the method runs.

`@PreFilter` is used when you want Spring Security to automatically remove unauthorized elements from input collections before business logic executes.