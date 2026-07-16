
## Definition

`@Secured` is a Spring Security annotation used for **role-based method authorization**.

It tells Spring:

> Only users having the specified role(s) can execute this method.

Package:

```java
import org.springframework.security.access.annotation.Secured;
```

---

# Why Do We Need @Secured?

Suppose you have:

```java
public void deleteEmployee() {
}
```

You want:

```text
Only ADMIN can delete employees.
```

Without security:

```text
Any authenticated user
can execute the method.
```

---

Solution:

```java
@Secured
```

---

Example:

```java
@Secured("ROLE_ADMIN")
public void deleteEmployee() {
}
```

---

# Prerequisite

Method security must be enabled.

```java
@Configuration

@EnableMethodSecurity(
    securedEnabled = true
)
public class SecurityConfig {
}
```

---

Without:

```java
securedEnabled = true
```

`@Secured` will not work.

---

# Basic Example

```java
@Service
public class EmployeeService {

    @Secured("ROLE_ADMIN")
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
Only ROLE_ADMIN
Can Execute
```

---

# What Happens Internally?

Method Call:

```java
employeeService.deleteEmployee();
```

---

Flow:

```text
Method Called
      ↓
Spring Security Intercepts
      ↓
Check User Roles
      ↓
Authorized?
      ↓
Execute Method
```

---

Unauthorized:

```text
AccessDeniedException
```

---

# Single Role Example

```java
@Secured(
 "ROLE_ADMIN"
)
public void deleteEmployee() {
}
```

---

Allowed:

```text
ROLE_ADMIN
```

---

Denied:

```text
ROLE_USER
ROLE_MANAGER
```

---

# Multiple Roles Example

```java
@Secured({
 "ROLE_ADMIN",
 "ROLE_MANAGER"
})
public void updateEmployee() {
}
```

---

Meaning:

```text
ADMIN OR MANAGER
```

---

Either role is sufficient.

---

# Complete Example

```java
@Service
public class EmployeeService {

    @Secured("ROLE_ADMIN")
    public void deleteEmployee() {

        System.out.println(
          "Deleted"
        );
    }

    @Secured({
      "ROLE_ADMIN",
      "ROLE_MANAGER"
    })
    public void updateEmployee() {

        System.out.println(
          "Updated"
        );
    }

    @Secured("ROLE_USER")
    public void viewEmployee() {

        System.out.println(
          "Viewed"
        );
    }
}
```

---

# Internal Workflow

Spring creates:

```text
AOP Proxy
```

---

Execution:

```text
Method Call
      ↓
Proxy Intercepts
      ↓
Role Validation
      ↓
Execute Method
```

---

# Role Prefix Requirement

Very Important Interview Question.

With:

```java
@Secured
```

you must specify:

```java
ROLE_
```

---

Correct:

```java
@Secured(
 "ROLE_ADMIN"
)
```

---

Wrong:

```java
@Secured(
 "ADMIN"
)
```

---

Will fail.

---

# @Secured vs @PreAuthorize

Interview Favorite.

---

## @Secured

Supports:

```text
Role Checks Only
```

---

Example:

```java
@Secured(
 "ROLE_ADMIN"
)
```

---

# @PreAuthorize

Supports:

```text
Roles
Authorities
Parameters
Custom Logic
SpEL
```

---

Example:

```java
@PreAuthorize(
 "hasRole('ADMIN')"
)
```

---

Comparison:

| Feature | @Secured | @PreAuthorize |
|----------|----------|--------------|
| Role Checks | ✅ | ✅ |
| Authorities | ❌ | ✅ |
| Method Parameters | ❌ | ✅ |
| SpEL Expressions | ❌ | ✅ |
| Custom Logic | ❌ | ✅ |

---

# Example of Limitation

Not possible:

```java
@Secured(
 "#id == authentication.name"
)
```

---

Because:

```java
@Secured
```

does not support:

```text
SpEL Expressions
```

---

Use:

```java
@PreAuthorize
```

instead.

---

# Using Authorities

Wrong:

```java
@Secured(
 "EMPLOYEE_READ"
)
```

---

`@Secured`

expects:

```text
Roles
```

---

Use:

```java
@PreAuthorize(
 "hasAuthority(
   'EMPLOYEE_READ'
 )"
)
```

---

instead.

---

# Real-World Example

Admin Service:

```java
@Service
public class AdminService {

    @Secured(
      "ROLE_ADMIN"
    )
    public void createUser() {

    }

    @Secured(
      "ROLE_ADMIN"
    )
    public void deleteUser() {

    }
}
```

---

Only administrators can execute.

---

# Access Denied Example

Current User:

```text
ROLE_USER
```

---

Method:

```java
@Secured(
 "ROLE_ADMIN"
)
public void deleteUser() {
}
```

---

Result:

```text
AccessDeniedException
```

---

Method never executes.

---

# Internal Components

`@Secured` uses:

```text
Method Security Interceptor
Authorization Manager
Spring AOP Proxy
```

---

Flow:

```text
Method Call
      ↓
Check Roles
      ↓
Authorized?
      ↓
Execute Method
```

---

# @Secured vs @RolesAllowed

Interview Question.

---

## @Secured

Spring-specific.

---

```java
@Secured(
 "ROLE_ADMIN"
)
```

---

Requires:

```text
ROLE_
```

prefix.

---

## @RolesAllowed

JSR-250 standard.

---

```java
@RolesAllowed(
 "ADMIN"
)
```

---

No explicit:

```text
ROLE_
```

required in the annotation value.

---

Comparison:

| Feature | @Secured | @RolesAllowed |
|----------|----------|---------------|
| Spring Specific | ✅ | ❌ |
| JSR-250 Standard | ❌ | ✅ |
| Requires ROLE_ Prefix | ✅ | Usually No |
| Role Checks | ✅ | ✅ |

---

# Common Mistakes

## Mistake 1

Missing securedEnabled

Wrong:

```java
@EnableMethodSecurity
```

---

Correct:

```java
@EnableMethodSecurity(
   securedEnabled = true
)
```

---

# Mistake 2

Missing ROLE_ Prefix

Wrong:

```java
@Secured("ADMIN")
```

---

Correct:

```java
@Secured("ROLE_ADMIN")
```

---

# Mistake 3

Using SpEL

Wrong:

```java
@Secured(
 "#id == authentication.name"
)
```

---

Not supported.

---

Use:

```java
@PreAuthorize
```

---

# Common Interview Questions

## What is @Secured?

Role-based method authorization annotation.

---

## Does It Support SpEL?

No.

---

## Can It Access Method Parameters?

No.

---

## Does It Support Authorities?

No.

Primarily role-based.

---

## Difference Between @Secured and @PreAuthorize?

`@PreAuthorize`

is much more powerful.

---

## Which One Is Preferred Today?

```java
@PreAuthorize
```

for most modern applications.

---

# Enterprise Best Practice

Use:

```java
@PreAuthorize
```

for:

```text
Complex Authorization
Permissions
Ownership Checks
Dynamic Rules
```

---

Use:

```java
@Secured
```

for:

```text
Simple Role Checks
Legacy Applications
```

---

Modern Spring applications usually prefer:

```java
@PreAuthorize
```

because of its flexibility.

---

# Key Points To Remember

- `@Secured` provides role-based method security.
- Requires `securedEnabled = true`.
- Uses Spring AOP proxies internally.
- Supports one or more roles.
- Requires `ROLE_` prefix.
- Does not support SpEL expressions.
- Cannot access method parameters.
- Simpler than `@PreAuthorize`.
- Throws `AccessDeniedException` when authorization fails.
- Frequently asked Spring Security interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Secured` | Simple Role-Based Security |
| `@PreAuthorize` | Advanced Authorization |
| `@PostAuthorize` | Authorization After Method |
| `@RolesAllowed` | JSR-250 Role Security |
| `@EnableMethodSecurity` | Enable Method Security |

---

# Interview Shortcut

```java
@Secured(
 "ROLE_ADMIN"
)
public void deleteEmployee() {
}
```

Meaning:

```text
Method Called
      ↓
Check User Role
      ↓
ROLE_ADMIN?
      ↓
Yes → Execute
No  → AccessDeniedException
```

Think of it as:

```java
if(user.hasRole("ROLE_ADMIN")) {
    deleteEmployee();
}
else {
    throw new AccessDeniedException();
}
```

Most common usage:

```text
Admin Operations
Manager Operations
Role-Based Access
Legacy Spring Security Applications
```

`@Secured` is a simple Spring Security annotation used to restrict method execution based on user roles, making it ideal for straightforward role-based authorization scenarios.