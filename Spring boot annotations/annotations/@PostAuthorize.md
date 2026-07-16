
## Definition

`@PostAuthorize` is a Spring Security annotation used to perform **authorization checks after a method executes**.

It tells Spring:

> Execute the method first, then evaluate the security expression using the returned value.

Package:

```java
import org.springframework.security.access.prepost.PostAuthorize;
```

---

# Why Do We Need @PostAuthorize?

Sometimes authorization depends on the data returned by the method.

Example:

```java
Employee employee = getEmployee(id);
```

You may want:

```text
User can access only
their own employee record.
```

To make that decision, Spring must first execute the method and obtain the returned object.

---

Solution:

```java
@PostAuthorize
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

`@PostAuthorize` will not work.

---

# What Happens Internally?

Method Call:

```java
employeeService.getEmployee(1L);
```

---

Flow:

```text
Method Called
      ↓
Method Executes
      ↓
Return Object Created
      ↓
Security Expression Evaluated
      ↓
Access Granted / Denied
```

---

Unlike:

```java
@PreAuthorize
```

the method is executed first.

---

# Basic Example

```java
@PostAuthorize(
  "hasRole('ADMIN')"
)
public Employee getEmployee(
       Long id) {

    return repository.findById(id);
}
```

---

Flow:

```text
Execute Method
      ↓
Return Employee
      ↓
Check ADMIN Role
      ↓
Allow / Deny
```

---

# Accessing Returned Object

Most important feature.

Spring exposes:

```java
returnObject
```

inside the expression.

---

Example:

```java
@PostAuthorize(
   "returnObject.owner ==
    authentication.name"
)
```

---

Meaning:

```text
User Can Access
Only Their Own Record
```

---

# Real Example

Employee:

```java
public class Employee {

    private String username;
}
```

---

Method:

```java
@PostAuthorize(
  "returnObject.username ==
   authentication.name"
)
public Employee getEmployee(
       Long id) {

    return repository.findById(id);
}
```

---

Logged In User:

```text
sumit
```

---

Returned Employee:

```text
username = sumit
```

---

Access:

```text
Allowed
```

---

Returned Employee:

```text
username = admin
```

---

Access:

```text
Denied
```

---

# Complete Example

```java
@Service
public class EmployeeService {

    @PostAuthorize(
      "returnObject.username ==
       authentication.name"
    )
    public Employee getEmployee(
           Long id) {

        return repository
                .findById(id)
                .orElseThrow();
    }
}
```

---

Flow:

```text
Fetch Employee
       ↓
Compare Owner
       ↓
Grant / Deny
```

---

# Using Roles

```java
@PostAuthorize(
 "hasRole('ADMIN')"
)
```

---

Meaning:

```text
Execute Method
Then Check ADMIN
```

---

Although possible, this is usually better with:

```java
@PreAuthorize
```

because it avoids unnecessary execution.

---

# Combining Conditions

AND:

```java
@PostAuthorize(
 "hasRole('ADMIN')
  and
  returnObject.active"
)
```

---

Both conditions required.

---

OR:

```java
@PostAuthorize(
 "hasRole('ADMIN')
  or
  returnObject.owner ==
  authentication.name"
)
```

---

Meaning:

```text
Admin
OR
Owner
```

---

# Using Authentication Object

Logged-in user:

```java
authentication.name
```

---

Example:

```java
@PostAuthorize(
 "returnObject.createdBy ==
  authentication.name"
)
```

---

Very common.

---

# Accessing Nested Properties

Returned Object:

```java
Employee
```

contains:

```java
Department
```

---

Example:

```java
@PostAuthorize(
 "returnObject.department
     .manager ==
  authentication.name"
)
```

---

Spring can navigate object properties.

---

# Null Safety Example

Potential issue:

```java
returnObject.owner
```

if:

```java
returnObject == null
```

---

Safer:

```java
@PostAuthorize(
 "returnObject != null
  and
  returnObject.owner ==
  authentication.name"
)
```

---

# Internal Components

`@PostAuthorize` uses:

```text
Method Security Interceptor
Authorization Manager
SpEL Engine
Spring AOP Proxy
```

---

Flow:

```text
Method Call
      ↓
Method Executes
      ↓
Return Value Obtained
      ↓
Expression Evaluated
      ↓
Allow / Deny
```

---

# @PostAuthorize vs @PreAuthorize

Interview Favorite.

---

## @PreAuthorize

Checks:

```text
Before Method Execution
```

---

Method may never execute.

---

Example:

```java
@PreAuthorize(
 "hasRole('ADMIN')"
)
```

---

# @PostAuthorize

Checks:

```text
After Method Execution
```

---

Can inspect:

```java
returnObject
```

---

Comparison:

| Feature | @PreAuthorize | @PostAuthorize |
|----------|---------------|---------------|
| Before Method | ✅ | ❌ |
| After Method | ❌ | ✅ |
| Access Parameters | ✅ | ✅ |
| Access Return Value | ❌ | ✅ |
| Prevent Method Execution | ✅ | ❌ |

---

# Real-World Example

Banking System:

```java
Account getAccount(id)
```

---

Returned:

```java
Account.owner
```

---

Security:

```java
@PostAuthorize(
 "returnObject.owner ==
  authentication.name"
)
```

---

Meaning:

```text
Users Can Access
Only Their Own Accounts
```

---

# Common Mistakes

## Mistake 1

Using @PostAuthorize For Simple Role Checks

Wrong:

```java
@PostAuthorize(
 "hasRole('ADMIN')"
)
```

---

Better:

```java
@PreAuthorize(
 "hasRole('ADMIN')"
)
```

---

Avoid unnecessary execution.

---

# Mistake 2

Forgetting EnableMethodSecurity

Without:

```java
@EnableMethodSecurity
```

---

Annotation ignored.

---

# Mistake 3

Ignoring Null Return Values

Wrong:

```java
returnObject.owner
```

---

If:

```java
returnObject == null
```

---

Can fail.

---

# Common Interview Questions

## What is @PostAuthorize?

Performs authorization after method execution.

---

## Why Use It?

When authorization depends on returned data.

---

## What Special Variable Is Available?

```java
returnObject
```

---

## Difference Between @PreAuthorize and @PostAuthorize?

### @PreAuthorize

```text
Checks Before Method
```

---

### @PostAuthorize

```text
Checks After Method
```

---

## Which One Is Faster?

```java
@PreAuthorize
```

because it can stop execution early.

---

## Can @PostAuthorize Access Returned Object?

Yes.

Using:

```java
returnObject
```

---

# Enterprise Best Practice

Use:

```java
@PostAuthorize
```

only when:

```text
Authorization Depends
On Returned Data
```

---

Examples:

```text
Employee Ownership
Account Ownership
Document Ownership
Profile Access
Customer Data Access
```

---

For simple role checks:

```java
@PreAuthorize
```

is preferred.

---

# Key Points To Remember

- `@PostAuthorize` checks authorization after method execution.
- Requires `@EnableMethodSecurity`.
- Uses Spring Expression Language (SpEL).
- Provides access to `returnObject`.
- Useful for ownership-based access control.
- Executes even if access is ultimately denied.
- Uses Spring AOP proxies internally.
- Slower than `@PreAuthorize` for simple checks.
- Ideal for returned-object validation.
- Frequently asked Spring Security interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@PreAuthorize` | Check Before Method |
| `@PostAuthorize` | Check After Method |
| `@Secured` | Role-Based Authorization |
| `@RolesAllowed` | JSR-250 Authorization |
| `@EnableMethodSecurity` | Enable Method Security |

---

# Interview Shortcut

```java
@PostAuthorize(
 "returnObject.owner ==
  authentication.name"
)
public Employee getEmployee(
       Long id) {

    return employee;
}
```

Meaning:

```text
Method Executes
      ↓
Employee Returned
      ↓
Check Owner
      ↓
Owner Match?
      ↓
Allow / Deny
```

Think of it as:

```java
Employee emp = getEmployee(id);

if(emp.getOwner()
      .equals(currentUser)) {

    return emp;
}

throw AccessDeniedException;
```

Most common production usage:

```text
Profile Ownership
Account Ownership
Document Ownership
Customer Data Security
```

`@PostAuthorize` is used when authorization decisions require access to the object returned by a method, enabling fine-grained, ownership-based security checks after execution.