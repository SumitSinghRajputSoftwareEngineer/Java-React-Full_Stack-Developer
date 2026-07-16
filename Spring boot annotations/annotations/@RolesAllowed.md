
## Definition

`@RolesAllowed` is a **JSR-250 standard security annotation** used to restrict access to methods based on user roles.

It tells Spring:

> Only users having one of the specified roles can execute this method.

Package:

```java
import jakarta.annotation.security.RolesAllowed;
```

(Spring Boot 3+)

Older versions:

```java
import javax.annotation.security.RolesAllowed;
```

---

# Why Do We Need @RolesAllowed?

Suppose you have:

```java
public void deleteEmployee() {
}
```

You want:

```text
Only ADMIN users
can execute this method.
```

Without security:

```text
Any authenticated user
can execute it.
```

---

Solution:

```java
@RolesAllowed
```

---

# Prerequisite

Method security must be enabled.

```java
@Configuration

@EnableMethodSecurity(
   jsr250Enabled = true
)
public class SecurityConfig {
}
```

---

Without:

```java
jsr250Enabled = true
```

the annotation won't work.

---

# Basic Example

```java
@Service
public class EmployeeService {

    @RolesAllowed("ADMIN")
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
Only ADMIN Role
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
Security Interceptor
      ↓
Role Validation
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
@RolesAllowed(
 "ADMIN"
)
public void deleteEmployee() {
}
```

---

Allowed:

```text
ADMIN
```

---

Denied:

```text
USER
MANAGER
```

---

# Multiple Roles Example

```java
@RolesAllowed({
 "ADMIN",
 "MANAGER"
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

Any listed role can access.

---

# Complete Example

```java
@Service
public class EmployeeService {

    @RolesAllowed("ADMIN")
    public void deleteEmployee() {

        System.out.println(
          "Deleted"
        );
    }

    @RolesAllowed({
      "ADMIN",
      "MANAGER"
    })
    public void updateEmployee() {

        System.out.println(
          "Updated"
        );
    }

    @RolesAllowed("USER")
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
Check User Roles
      ↓
Authorized?
      ↓
Execute Method
```

---

# Role Prefix Behavior

Interview Question.

---

With:

```java
@RolesAllowed("ADMIN")
```

Spring internally checks:

```text
ROLE_ADMIN
```

---

You typically write:

```java
@RolesAllowed("ADMIN")
```

---

Not:

```java
@RolesAllowed("ROLE_ADMIN")
```

---

# @RolesAllowed vs @Secured

Interview Favorite.

---

## @RolesAllowed

JSR-250 Standard.

---

```java
@RolesAllowed("ADMIN")
```

---

Portable across frameworks.

---

# @Secured

Spring-specific.

---

```java
@Secured("ROLE_ADMIN")
```

---

Requires:

```text
ROLE_
```

prefix.

---

Comparison:

| Feature | @RolesAllowed | @Secured |
|----------|--------------|-----------|
| JSR-250 Standard | ✅ | ❌ |
| Spring Specific | ❌ | ✅ |
| Role Checks | ✅ | ✅ |
| Requires ROLE_ Prefix | Usually No | Yes |
| Portable | ✅ | ❌ |

---

# @RolesAllowed vs @PreAuthorize

Interview Favorite.

---

## @RolesAllowed

Supports:

```text
Simple Role Checks
```

---

Example:

```java
@RolesAllowed(
 "ADMIN"
)
```

---

# @PreAuthorize

Supports:

```text
Roles
Authorities
Parameters
SpEL
Custom Logic
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

| Feature | @RolesAllowed | @PreAuthorize |
|----------|--------------|--------------|
| Role Checks | ✅ | ✅ |
| SpEL | ❌ | ✅ |
| Method Parameters | ❌ | ✅ |
| Authorities | ❌ | ✅ |
| Custom Logic | ❌ | ✅ |

---

# Example Limitation

Not possible:

```java
@RolesAllowed(
 "#id == authentication.name"
)
```

---

Because:

```java
@RolesAllowed
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

# Real-World Example

Admin Operations:

```java
@Service
public class AdminService {

    @RolesAllowed(
      "ADMIN"
    )
    public void createUser() {
    }

    @RolesAllowed(
      "ADMIN"
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
@RolesAllowed(
 "ADMIN"
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

`@RolesAllowed` uses:

```text
Method Security Interceptor
Authorization Manager
Spring AOP Proxy
```

---

Execution:

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

# Combining Multiple Roles

```java
@RolesAllowed({
 "ADMIN",
 "MANAGER",
 "SUPERVISOR"
})
```

---

Meaning:

```text
ADMIN OR MANAGER OR SUPERVISOR
```

---

# Common Mistakes

## Mistake 1

Forgetting jsr250Enabled

Wrong:

```java
@EnableMethodSecurity
```

---

Correct:

```java
@EnableMethodSecurity(
   jsr250Enabled = true
)
```

---

# Mistake 2

Using SpEL

Wrong:

```java
@RolesAllowed(
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

# Mistake 3

Using Authorities

Wrong:

```java
@RolesAllowed(
 "EMPLOYEE_READ"
)
```

---

Designed for:

```text
Roles
```

not arbitrary authorities.

---

# Common Interview Questions

## What is @RolesAllowed?

A JSR-250 role-based authorization annotation.

---

## Does It Support SpEL?

No.

---

## Can It Access Method Parameters?

No.

---

## Difference Between @RolesAllowed and @Secured?

`@RolesAllowed`

```text
Standard Annotation
```

---

`@Secured`

```text
Spring-Specific Annotation
```

---

## Which One Is More Portable?

```java
@RolesAllowed
```

because it is part of JSR-250.

---

## Difference Between @RolesAllowed and @PreAuthorize?

`@PreAuthorize`

is much more powerful and flexible.

---

# Enterprise Best Practice

Use:

```java
@RolesAllowed
```

when:

```text
Following JSR-250 Standards
Simple Role-Based Security
Framework Portability Matters
```

---

Use:

```java
@PreAuthorize
```

when:

```text
Complex Authorization
Permissions
Ownership Checks
Custom Rules
```

---

Modern Spring projects typically prefer:

```java
@PreAuthorize
```

for advanced authorization.

---

# Key Points To Remember

- `@RolesAllowed` is a JSR-250 standard annotation.
- Requires `jsr250Enabled = true`.
- Provides role-based method security.
- Supports one or multiple roles.
- Uses Spring AOP proxies internally.
- Does not support SpEL.
- Cannot access method parameters.
- More portable than `@Secured`.
- Throws `AccessDeniedException` on failure.
- Frequently asked Spring Security interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@RolesAllowed` | JSR-250 Role Security |
| `@Secured` | Spring Role Security |
| `@PreAuthorize` | Advanced Authorization |
| `@PostAuthorize` | Post-Execution Authorization |
| `@EnableMethodSecurity` | Enable Method Security |

---

# Interview Shortcut

```java
@RolesAllowed(
 "ADMIN"
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
ADMIN?
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
Role-Based Access
Admin Operations
Manager Operations
JSR-250 Standard Security
```

`@RolesAllowed` is a standard Java security annotation used to restrict method execution based on roles, offering a portable alternative to Spring-specific role-based annotations.