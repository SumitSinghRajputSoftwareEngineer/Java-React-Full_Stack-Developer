
## Definition

`@PreAuthorize` is a Spring Security annotation used to perform **authorization checks before a method executes**.

It tells Spring:

> Evaluate the security expression first. If it passes, execute the method; otherwise deny access.

Package:

```java
import org.springframework.security.access.prepost.PreAuthorize;
```

---

# Why Do We Need @PreAuthorize?

Suppose you have:

```java
public void deleteEmployee(Long id) {
    // delete employee
}
```

You don't want every authenticated user to delete employees.

Only:

```text
ADMIN
```

should be allowed.

---

Solution:

```java
@PreAuthorize
```

---

Example:

```java
@PreAuthorize(
   "hasRole('ADMIN')"
)
public void deleteEmployee(Long id) {
}
```

---

# Prerequisite

`@PreAuthorize` works only when method security is enabled.

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

the annotation is ignored.

---

# What Happens Internally?

Method Call:

```java
employeeService.deleteEmployee(1L);
```

---

Flow:

```text
Method Called
      ↓
Spring AOP Proxy
      ↓
Evaluate Security Expression
      ↓
Allowed?
      ↓
Execute Method
```

---

If denied:

```text
AccessDeniedException
```

---

# Basic Example

```java
@Service
public class EmployeeService {

    @PreAuthorize(
      "hasRole('ADMIN')"
    )
    public void deleteEmployee() {

        System.out.println(
           "Deleted"
        );
    }
}
```

---

Meaning:

```text
Only ADMIN can execute.
```

---

# Role-Based Authorization

## Single Role

```java
@PreAuthorize(
   "hasRole('ADMIN')"
)
```

---

Current user must have:

```text
ROLE_ADMIN
```

---

Spring automatically adds:

```text
ROLE_
```

prefix.

---

# Multiple Roles

```java
@PreAuthorize(
   "hasAnyRole(
      'ADMIN',
      'MANAGER'
   )"
)
```

---

Meaning:

```text
ADMIN OR MANAGER
```

---

# Authority-Based Authorization

Instead of roles:

```java
@PreAuthorize(
   "hasAuthority('EMPLOYEE_READ')"
)
```

---

Meaning:

```text
Must have EMPLOYEE_READ authority.
```

---

# Multiple Authorities

```java
@PreAuthorize(
   "hasAnyAuthority(
      'EMPLOYEE_READ',
      'EMPLOYEE_WRITE'
   )"
)
```

---

Meaning:

```text
Any one authority is enough.
```

---

# Authentication Check

Only authenticated users:

```java
@PreAuthorize(
   "isAuthenticated()"
)
```

---

Meaning:

```text
Login Required
```

---

# Anonymous User Check

```java
@PreAuthorize(
   "isAnonymous()"
)
```

---

Meaning:

```text
Only Guest Users
```

---

# Permit Everyone

```java
@PreAuthorize(
   "permitAll()"
)
```

---

Meaning:

```text
Anyone Can Access
```

---

# Deny Everyone

```java
@PreAuthorize(
   "denyAll()"
)
```

---

Meaning:

```text
Nobody Can Access
```

---

# Using Method Parameters

Very Important Interview Topic.

---

Method:

```java
public Employee getEmployee(
       Long id) {
}
```

---

Security:

```java
@PreAuthorize(
   "#id == 1"
)
```

---

Meaning:

```text
Only ID = 1 allowed
```

---

Spring can access:

```java
#id
```

directly.

---

# Example

```java
@PreAuthorize(
   "#employeeId > 0"
)
public Employee getEmployee(
      Long employeeId
) {
}
```

---

Validates parameter before execution.

---

# Checking Logged-In User

Method:

```java
public User profile(
       String username) {
}
```

---

Security:

```java
@PreAuthorize(
   "#username ==
    authentication.name"
)
```

---

Meaning:

```text
User Can Access
Only Their Own Profile
```

---

# Example

Logged in:

```text
sumit
```

---

Request:

```java
profile("sumit")
```

---

Allowed.

---

Request:

```java
profile("admin")
```

---

Denied.

---

# Combining Conditions

AND:

```java
@PreAuthorize(
   "hasRole('ADMIN')
    and
    hasAuthority('WRITE')"
)
```

---

Both required.

---

OR:

```java
@PreAuthorize(
   "hasRole('ADMIN')
    or
    hasRole('MANAGER')"
)
```

---

Any one role required.

---

NOT:

```java
@PreAuthorize(
   "!hasRole('GUEST')"
)
```

---

Guest users denied.

---

# Using Custom Service

Enterprise-Level Feature.

---

Security Service:

```java
@Service
public class PermissionService {

    public boolean canEdit(
           Long id) {

        return true;
    }
}
```

---

Usage:

```java
@PreAuthorize(
 "@permissionService
    .canEdit(#id)"
)
public void editEmployee(
        Long id) {
}
```

---

Flow:

```text
Method Call
      ↓
Permission Service
      ↓
True / False
      ↓
Execute / Deny
```

---

Very common in production.

---

# Complete Example

```java
@Service
public class EmployeeService {

    @PreAuthorize(
      "hasRole('ADMIN')"
    )
    public void deleteEmployee(
            Long id) {

        System.out.println(
          "Deleted"
        );
    }

    @PreAuthorize(
      "hasAnyRole(
         'ADMIN',
         'MANAGER'
      )"
    )
    public void updateEmployee() {

        System.out.println(
          "Updated"
        );
    }
}
```

---

# Internal Components

`@PreAuthorize` uses:

```text
Method Security Interceptor
Authorization Manager
Spring Expression Language (SpEL)
Spring AOP Proxy
```

---

Execution Flow:

```text
Method Call
      ↓
Proxy Intercepts
      ↓
Evaluate Expression
      ↓
Authorized?
      ↓
Execute Method
```

---

# @PreAuthorize vs @PostAuthorize

Interview Favorite.

---

## @PreAuthorize

Checks:

```text
Before Method Execution
```

---

Example:

```java
@PreAuthorize(
 "hasRole('ADMIN')"
)
```

---

Method may never execute.

---

# @PostAuthorize

Checks:

```text
After Method Execution
```

---

Example:

```java
@PostAuthorize(
 "returnObject.owner ==
  authentication.name"
)
```

---

Can inspect returned object.

---

Comparison:

| Feature | @PreAuthorize | @PostAuthorize |
|----------|---------------|---------------|
| Before Execution | ✅ | ❌ |
| After Execution | ❌ | ✅ |
| Access Return Value | ❌ | ✅ |
| Access Parameters | ✅ | ✅ |

---

# Common Mistakes

## Mistake 1

Forgetting EnableMethodSecurity

Wrong:

```java
@PreAuthorize(...)
```

without:

```java
@EnableMethodSecurity
```

---

Annotation won't work.

---

# Mistake 2

Using ROLE_ADMIN

Wrong:

```java
hasRole('ROLE_ADMIN')
```

---

Correct:

```java
hasRole('ADMIN')
```

---

Spring automatically adds:

```text
ROLE_
```

---

# Mistake 3

Self Invocation

```java
this.deleteEmployee();
```

---

Proxy bypassed.

---

Security not applied.

---

# Common Interview Questions

## What is @PreAuthorize?

Performs authorization before method execution.

---

## Does Method Execute If Authorization Fails?

No.

---

## Can It Access Method Parameters?

Yes.

Using:

```java
#parameterName
```

---

## Can It Access Logged-In User?

Yes.

Using:

```java
authentication
```

---

## Difference Between hasRole and hasAuthority?

### hasRole

Automatically adds:

```text
ROLE_
```

---

### hasAuthority

Uses exact value.

---

# Enterprise Best Practice

Use:

```java
@PreAuthorize
```

on:

```text
Service Layer
Business Methods
Critical Operations
```

---

Examples:

```java
Delete Employee
Approve Loan
Transfer Money
Update Salary
Generate Reports
```

---

Combine with:

```java
@EnableMethodSecurity
```

for robust authorization.

---

# Key Points To Remember

- `@PreAuthorize` checks authorization before method execution.
- Requires `@EnableMethodSecurity`.
- Uses Spring Expression Language (SpEL).
- Supports roles and authorities.
- Can access method parameters.
- Can access authentication details.
- Supports custom permission services.
- Uses Spring AOP proxies internally.
- Throws `AccessDeniedException` on failure.
- Frequently asked Spring Security interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@PreAuthorize` | Check Before Method |
| `@PostAuthorize` | Check After Method |
| `@Secured` | Role-Based Check |
| `@RolesAllowed` | JSR-250 Role Check |
| `@EnableMethodSecurity` | Enable Method Security |

---

# Interview Shortcut

```java
@PreAuthorize(
 "hasRole('ADMIN')"
)
public void deleteEmployee() {
}
```

Meaning:

```text
Method Called
      ↓
Security Check
      ↓
ADMIN?
      ↓
Yes → Execute
No  → AccessDeniedException
```

Another common example:

```java
@PreAuthorize(
 "#username ==
  authentication.name"
)
```

Meaning:

```text
User Can Access
Only Their Own Data
```

`@PreAuthorize` is the most commonly used method-level authorization annotation in Spring Security because it allows powerful role checks, authority checks, parameter validation, and custom permission logic before a method executes.