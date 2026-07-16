
## Definition

`@AuthenticationPrincipal` is a Spring Security annotation used to inject the **currently authenticated user (principal)** directly into a controller method parameter.

It tells Spring:

> Give me the logged-in user's details from the Security Context.

Package:

```java
import org.springframework.security.core.annotation.AuthenticationPrincipal;
```

---

# Why Do We Need @AuthenticationPrincipal?

Without it, to get the current user:

```java
Authentication authentication =
    SecurityContextHolder
      .getContext()
      .getAuthentication();

UserDetails user =
    (UserDetails)
    authentication.getPrincipal();
```

---

This is:

```text
Verbose
Repeated Everywhere
Harder To Read
```

---

Solution:

```java
@AuthenticationPrincipal
```

---

# Basic Example

```java
@GetMapping("/profile")
public String profile(

   @AuthenticationPrincipal
   UserDetails user

) {

    return user.getUsername();
}
```

---

If logged-in user is:

```text
sumit
```

---

Response:

```text
sumit
```

---

# What Is Principal?

In Spring Security:

```text
Principal = Logged-In User
```

---

After authentication:

```java
Authentication
```

contains:

```java
Principal
Credentials
Authorities
```

---

Example:

```java
Authentication auth =
    SecurityContextHolder
      .getContext()
      .getAuthentication();
```

---

Principal:

```java
auth.getPrincipal();
```

---

Typically:

```java
UserDetails
```

or

```java
CustomUserDetails
```

---

# What Happens Internally?

Request:

```http
GET /profile
```

---

Flow:

```text
Request
   ↓
Authentication Filter
   ↓
Security Context
   ↓
Authentication Object
   ↓
Principal Extracted
   ↓
Method Parameter Injected
```

---

# Example Using UserDetails

```java
@GetMapping("/profile")
public String profile(

   @AuthenticationPrincipal
   UserDetails user

) {

    return user.getUsername();
}
```

---

Logged In User:

```text
sumit
```

---

Result:

```text
sumit
```

---

# Custom User Example

Most common in enterprise applications.

---

Custom User:

```java
public class CustomUserDetails
      implements UserDetails {

    private Long id;

    private String username;

    private String email;
}
```

---

Controller:

```java
@GetMapping("/me")
public String me(

   @AuthenticationPrincipal
   CustomUserDetails user

) {

    return user.getEmail();
}
```

---

Response:

```text
sumit@gmail.com
```

---

# Complete Example

## Custom User

```java
public class CustomUserDetails
       implements UserDetails {

    private Long id;

    private String username;

    private String email;
}
```

---

## Controller

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping("/me")
    public String currentUser(

      @AuthenticationPrincipal
      CustomUserDetails user

    ) {

        return user.getUsername();
    }
}
```

---

Output:

```text
sumit
```

---

# Getting User ID Directly

```java
@GetMapping("/profile")
public Long profile(

   @AuthenticationPrincipal
   CustomUserDetails user

) {

    return user.getId();
}
```

---

Very common.

---

# Using Expression Attribute

Useful feature.

---

Instead of:

```java
user.getId()
```

inside method.

---

Use:

```java
@GetMapping("/profile")
public Long profile(

 @AuthenticationPrincipal(
    expression = "id"
 )
 Long id

) {

    return id;
}
```

---

Spring automatically extracts:

```java
principal.getId()
```

---

# Example

Custom Principal:

```java
public class UserPrincipal {

    private Long id;
}
```

---

Controller:

```java
@GetMapping("/me")
public Long me(

 @AuthenticationPrincipal(
     expression = "id"
 )
 Long id

) {

    return id;
}
```

---

Result:

```text
1
```

---

# Using JWT Authentication

Very common.

---

JWT Filter:

```java
UsernamePasswordAuthenticationToken
```

stores principal.

---

Controller:

```java
@GetMapping("/profile")
public UserPrincipal profile(

 @AuthenticationPrincipal
 UserPrincipal principal

) {

    return principal;
}
```

---

Spring injects JWT-authenticated user.

---

# Without @AuthenticationPrincipal

Traditional way:

```java
Authentication auth =

 SecurityContextHolder
    .getContext()
    .getAuthentication();

UserDetails user =

 (UserDetails)
 auth.getPrincipal();
```

---

With:

```java
@AuthenticationPrincipal
```

---

Cleaner:

```java
public String profile(

 @AuthenticationPrincipal
 UserDetails user

)
```

---

# Using Principal Interface

Alternative:

```java
@GetMapping("/profile")
public String profile(
       Principal principal
) {

    return principal.getName();
}
```

---

Works.

---

But gives only:

```text
Username
```

---

Not full custom user object.

---

# Using Authentication

Alternative:

```java
@GetMapping("/profile")
public String profile(
      Authentication auth
) {

    return auth.getName();
}
```

---

Gives access to:

```text
Principal
Authorities
Credentials
```

---

# Comparison

| Approach | Access |
|-----------|---------|
| Principal | Username |
| Authentication | Full Authentication |
| @AuthenticationPrincipal | Current User Object |

---

# Internal Components

Uses:

```text
SecurityContextHolder
Authentication
Principal
Argument Resolver
```

---

Execution:

```text
Request
   ↓
Authentication Success
   ↓
Security Context Updated
   ↓
Controller Called
   ↓
Principal Injected
```

---

# Common Mistakes

## Mistake 1

Wrong Principal Type

Example:

```java
@AuthenticationPrincipal
CustomUser user
```

---

But actual principal:

```java
UserDetails
```

---

Causes:

```text
ClassCastException
```

---

# Mistake 2

Anonymous User

If endpoint:

```text
permitAll()
```

---

User may be:

```text
null
```

---

Check:

```java
if(user != null)
```

---

# Mistake 3

Confusing Principal With User Entity

Principal:

```text
Security User
```

---

Entity:

```text
Database User
```

---

Not always same object.

---

# @AuthenticationPrincipal vs Principal

Interview Favorite.

---

## Principal

```java
Principal principal
```

---

Provides:

```text
Username
```

---

## @AuthenticationPrincipal

```java
@AuthenticationPrincipal
CustomUserDetails user
```

---

Provides:

```text
Entire User Object
```

---

# @AuthenticationPrincipal vs Authentication

## Authentication

Provides:

```text
Principal
Authorities
Credentials
Details
```

---

## @AuthenticationPrincipal

Provides:

```text
Principal Only
```

---

Comparison:

| Feature | Authentication | @AuthenticationPrincipal |
|----------|---------------|--------------------------|
| Principal | ✅ | ✅ |
| Authorities | ✅ | ❌ |
| Credentials | ✅ | ❌ |
| Simplicity | ❌ | ✅ |

---

# Common Interview Questions

## What is @AuthenticationPrincipal?

Injects currently authenticated user.

---

## Where Does It Get Data From?

```java
SecurityContextHolder
```

---

## Can It Inject Custom UserDetails?

Yes.

Very common.

---

## What Happens If User Is Not Authenticated?

Usually:

```java
null
```

or access denied depending on configuration.

---

## Difference Between Principal and @AuthenticationPrincipal?

Principal:

```text
Username
```

---

@AuthenticationPrincipal:

```text
Complete User Object
```

---

# Enterprise Best Practice

Create:

```java
CustomUserDetails
```

---

Store:

```java
User ID
Username
Email
Roles
```

---

Use:

```java
@AuthenticationPrincipal
```

instead of repeatedly calling:

```java
SecurityContextHolder
```

---

Cleaner and easier to maintain.

---

# Key Points To Remember

- `@AuthenticationPrincipal` injects the logged-in user.
- Reads principal from Spring Security Context.
- Eliminates manual SecurityContextHolder code.
- Works with UserDetails and custom principals.
- Supports SpEL expressions.
- Commonly used in JWT-based applications.
- Uses Spring MVC argument resolvers internally.
- Can inject user ID, email, username, etc.
- Cleaner than using Authentication directly.
- Frequently asked Spring Security interview topic.

---

# Quick Comparison

| Approach | What You Get |
|------------|-------------|
| Principal | Username |
| Authentication | Full Authentication Object |
| @AuthenticationPrincipal | Logged-In User Object |

---

# Interview Shortcut

```java
@GetMapping("/me")
public String me(

 @AuthenticationPrincipal
 UserDetails user

) {

    return user.getUsername();
}
```

Meaning:

```text
Request Arrives
      ↓
User Authenticated
      ↓
Principal Stored
      ↓
Spring Injects Principal
      ↓
Method Uses User Directly
```

Equivalent to:

```java
Authentication auth =
    SecurityContextHolder
      .getContext()
      .getAuthentication();

UserDetails user =
    (UserDetails)
    auth.getPrincipal();
```

but much cleaner.

`@AuthenticationPrincipal` is the preferred Spring Security annotation for directly accessing the currently authenticated user inside controllers without manually interacting with the Security Context.