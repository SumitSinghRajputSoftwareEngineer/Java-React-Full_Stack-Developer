
## Definition

`@EnableMethodSecurity` is a Spring Security annotation used to enable **method-level security**.

It tells Spring:

> Apply security rules directly on methods using annotations such as `@PreAuthorize`, `@PostAuthorize`, `@Secured`, and `@RolesAllowed`.

Package:

```java
import org.springframework.security.config.annotation.method.configuration.EnableMethodSecurity;
```

---

# Why Do We Need @EnableMethodSecurity?

Suppose your API is secured:

```java
GET /employees
```

Only authenticated users can access it.

But what if:

```java
Admin can delete employees
User cannot delete employees
```

We need security at the method level.

---

Without Method Security

```java
@Service
public class EmployeeService {

    public void deleteEmployee(Long id) {

        // delete employee
    }
}
```

---

Anyone reaching this method can execute it.

---

Solution:

```java
@EnableMethodSecurity
```

---

Then:

```java
@PreAuthorize("hasRole('ADMIN')")
```

---

# Basic Example

## Security Configuration

```java
@Configuration

@EnableWebSecurity

@EnableMethodSecurity
public class SecurityConfig {
}
```

---

Method security is now enabled.

---

# Using @PreAuthorize

Example:

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
}
```

---

Meaning:

```text
Only ADMIN Can Execute
```

---

# What Happens Internally?

Method Call:

```java
employeeService.deleteEmployee(1L);
```

---

Flow:

```text
Proxy Intercepts
       ↓
Check Security Rule
       ↓
Authorized?
       ↓
Execute Method
```

---

If unauthorized:

```text
AccessDeniedException
```

---

# Most Common Annotations Enabled

`@EnableMethodSecurity` enables:

```java
@PreAuthorize
@PostAuthorize
@Secured
@RolesAllowed
```

---

# @PreAuthorize

Checks before method execution.

---

```java
@PreAuthorize(
 "hasRole('ADMIN')"
)
public void delete() {
}
```

---

Meaning:

```text
Check First
Execute Later
```

---

# @PostAuthorize

Checks after method execution.

---

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

---

Flow:

```text
Execute Method
      ↓
Check Result
      ↓
Allow / Deny
```

---

# @Secured

Role-based security.

---

```java
@Secured(
 "ROLE_ADMIN"
)
public void delete() {
}
```

---

Equivalent to:

```java
@PreAuthorize(
 "hasRole('ADMIN')"
)
```

---

# @RolesAllowed

JSR-250 standard.

---

```java
@RolesAllowed(
 "ADMIN"
)
public void delete() {
}
```

---

Java standard security annotation.

---

# Role-Based Example

```java
@Service
public class EmployeeService {

    @PreAuthorize(
      "hasRole('ADMIN')"
    )
    public void deleteEmployee() {

    }

    @PreAuthorize(
      "hasRole('USER')"
    )
    public void viewEmployee() {

    }
}
```

---

Result:

```text
ADMIN → deleteEmployee()
USER  → viewEmployee()
```

---

# Multiple Roles

```java
@PreAuthorize(
 "hasAnyRole(
   'ADMIN',
   'MANAGER'
 )"
)
public void updateEmployee() {
}
```

---

Meaning:

```text
ADMIN OR MANAGER
```

---

# Checking Username

```java
@PreAuthorize(
 "authentication.name == #username"
)
public void profile(
       String username) {
}
```

---

Meaning:

```text
User Can Access Only Own Profile
```

---

# Checking Method Parameters

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

Spring can access parameters.

---

# Custom Permission Example

```java
@PreAuthorize(
 "@permissionService
    .canEdit(#employeeId)"
)
```

---

Calls:

```java
permissionService.canEdit()
```

before execution.

---

Very common in enterprise projects.

---

# Complete Example

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

# Request Flow Internally

Method Call:

```java
deleteEmployee()
```

---

Flow:

```text
Spring Proxy
      ↓
Security Expression
      ↓
Role Validation
      ↓
Method Execution
```

---

Unauthorized:

```text
AccessDeniedException
```

---

# Enable Specific Features

Example:

```java
@EnableMethodSecurity(
   securedEnabled = true
)
```

---

Enables:

```java
@Secured
```

---

Example:

```java
@EnableMethodSecurity(
   jsr250Enabled = true
)
```

---

Enables:

```java
@RolesAllowed
```

---

Example:

```java
@EnableMethodSecurity(
   prePostEnabled = true
)
```

---

Enables:

```java
@PreAuthorize
@PostAuthorize
```

---

In Spring Security 6:

```java
@EnableMethodSecurity
```

enables pre/post annotations by default.

---

# Internal Components

Activates:

```text
MethodSecurityInterceptor
AuthorizationManager
SecurityExpressionHandler
AOP Proxies
```

---

Method security works using:

```text
Spring AOP
```

---

# @EnableMethodSecurity vs @EnableWebSecurity

Interview Favorite.

---

## @EnableWebSecurity

Secures:

```text
URLs
Endpoints
HTTP Requests
```

---

Example:

```java
/api/admin/**
```

---

# @EnableMethodSecurity

Secures:

```text
Methods
Service Layer
Business Logic
```

---

Example:

```java
deleteEmployee()
```

---

Comparison:

| Feature | Web Security | Method Security |
|----------|-------------|----------------|
| URL Protection | ✅ | ❌ |
| Service Method Protection | ❌ | ✅ |
| Uses Filters | ✅ | ❌ |
| Uses AOP Proxy | ❌ | ✅ |

---

# Common Mistakes

## Mistake 1

Forgetting Annotation

Wrong:

```java
@PreAuthorize(...)
```

without:

```java
@EnableMethodSecurity
```

---

Security won't work.

---

# Mistake 2

Using ROLE_ADMIN Instead Of ADMIN

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

prefix.

---

# Mistake 3

Method Not Called Through Proxy

Example:

```java
this.deleteEmployee();
```

---

Method security bypassed.

---

Because Spring AOP proxy isn't used.

---

# Common Interview Questions

## What is @EnableMethodSecurity?

Enables method-level security annotations.

---

## What annotations does it support?

```java
@PreAuthorize
@PostAuthorize
@Secured
@RolesAllowed
```

---

## Difference Between @EnableMethodSecurity and @EnableWebSecurity?

Web Security:

```text
URL Level
```

---

Method Security:

```text
Method Level
```

---

## Does It Use Filters?

No.

Uses:

```text
Spring AOP Proxies
```

---

## Which Is More Secure?

Method security.

Because it protects business logic directly.

---

# Enterprise Best Practice

Use both:

```java
@EnableWebSecurity

@EnableMethodSecurity
```

---

Layered Security:

```text
Request Level Security
        +
Method Level Security
```

---

Example:

```java
URL Protection
Role Checks
Ownership Validation
Permission Checks
```

---

This is the standard enterprise approach.

---

# Key Points To Remember

- `@EnableMethodSecurity` enables method-level security.
- Supports `@PreAuthorize`, `@PostAuthorize`, `@Secured`, and `@RolesAllowed`.
- Uses Spring AOP proxies internally.
- Protects service-layer methods.
- Works independently of URL security.
- Supports SpEL expressions.
- Supports role and permission checks.
- Frequently used in enterprise applications.
- Often combined with `@EnableWebSecurity`.
- Frequently asked Spring Security interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EnableWebSecurity` | Secure HTTP Requests |
| `@EnableMethodSecurity` | Secure Methods |
| `@PreAuthorize` | Check Before Execution |
| `@PostAuthorize` | Check After Execution |
| `@Secured` | Role-Based Security |
| `@RolesAllowed` | JSR-250 Security |

---

# Interview Shortcut

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {
}
```

Example:

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
Spring AOP Proxy
      ↓
Security Check
      ↓
Authorized?
      ↓
Execute Method
```

Typical production usage:

```text
Role Validation
Permission Checks
Ownership Checks
Business Rule Security
```

`@EnableMethodSecurity` is the annotation that activates Spring Security at the service/business-method level, making it possible to secure individual methods with fine-grained authorization rules.