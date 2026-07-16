
## Definition

`@EnableWebSecurity` is a Spring Security annotation used to enable and configure **web application security**.

It tells Spring:

> Activate Spring Security's web security infrastructure and allow custom security configurations.

Package:

```java
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
```

---

# Why Do We Need @EnableWebSecurity?

Without security:

```text
Anyone can access APIs
Anyone can access pages
No Authentication
No Authorization
```

Example:

```java
GET /admin
```

Anyone can access it.

---

Solution:

```java
@EnableWebSecurity
```

---

This activates Spring Security filters and security configurations.

---

# Basic Example

```java
@Configuration

@EnableWebSecurity
public class SecurityConfig {
}
```

---

Once enabled, Spring Security starts protecting endpoints.

---

# What Happens Internally?

When Spring starts:

```java
@EnableWebSecurity
```

registers security infrastructure such as:

```text
Security Filter Chain
Authentication Manager
Authorization Manager
Security Context
Password Encoders
```

---

Flow:

```text
Request
   ↓
Security Filters
   ↓
Authentication
   ↓
Authorization
   ↓
Controller
```

---

# Spring Security Filter Chain

Most Important Interview Topic.

Every request passes through:

```text
Security Filter Chain
```

Example:

```text
Browser
   ↓
Security Filters
   ↓
Controller
```

---

Without authentication:

```text
401 Unauthorized
```

---

# Basic Security Configuration

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(
            HttpSecurity http)
            throws Exception {

        http
            .authorizeHttpRequests(auth ->
                auth.anyRequest()
                    .authenticated()
            )
            .formLogin();

        return http.build();
    }
}
```

---

Meaning:

```text
Every Request Requires Login
```

---

# Allow Public APIs

Example:

```java
@Bean
public SecurityFilterChain filterChain(
        HttpSecurity http)
        throws Exception {

    http
      .authorizeHttpRequests(auth ->

         auth

          .requestMatchers(
             "/public/**"
          ).permitAll()

          .anyRequest()
          .authenticated()
      );

    return http.build();
}
```

---

Result:

```text
/public/**
```

Accessible by everyone.

---

Others:

```text
Login Required
```

---

# Role-Based Security

Example:

```java
http
.authorizeHttpRequests(auth ->

   auth

   .requestMatchers("/admin/**")
   .hasRole("ADMIN")

   .requestMatchers("/user/**")
   .hasRole("USER")

   .anyRequest()
   .authenticated()
);
```

---

Meaning:

```text
/admin/** → ADMIN
/user/** → USER
```

---

# In-Memory Authentication

Example:

```java
@Bean
public UserDetailsService users() {

    UserDetails admin =

        User.withUsername("admin")
            .password("{noop}123")
            .roles("ADMIN")
            .build();

    return new InMemoryUserDetailsManager(
            admin
    );
}
```

---

Login:

```text
Username: admin
Password: 123
```

---

# Password Encoding

Never store plain passwords.

---

Use:

```java
@Bean
public PasswordEncoder passwordEncoder() {

    return new BCryptPasswordEncoder();
}
```

---

Spring recommends:

```java
BCrypt
```

---

# JWT Security Example

Very common in Spring Boot APIs.

---

```java
http
.csrf(csrf -> csrf.disable())

.sessionManagement(session ->

    session.sessionCreationPolicy(
       SessionCreationPolicy.STATELESS
    )
);
```

---

Meaning:

```text
No Session
Use JWT Token
```

---

# Disable CSRF

For REST APIs:

```java
http.csrf(csrf -> csrf.disable());
```

---

Common in:

```text
Microservices
REST APIs
JWT Authentication
```

---

# SecurityFilterChain

Modern Spring Security.

---

Before Spring Security 5.7:

```java
extends WebSecurityConfigurerAdapter
```

---

Deprecated.

---

Now:

```java
@Bean
SecurityFilterChain
```

---

Recommended approach.

---

# Complete Example

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    SecurityFilterChain filterChain(
         HttpSecurity http)
         throws Exception {

        http

        .authorizeHttpRequests(auth ->

            auth

            .requestMatchers(
                "/public/**"
            ).permitAll()

            .requestMatchers(
                "/admin/**"
            ).hasRole("ADMIN")

            .anyRequest()
            .authenticated()
        )

        .formLogin();

        return http.build();
    }
}
```

---

# Request Flow Internally

Request:

```text
GET /admin/dashboard
```

---

Flow:

```text
Request
   ↓
Security Filter Chain
   ↓
Authentication Check
   ↓
Role Check
   ↓
Controller
```

---

If unauthorized:

```text
403 Forbidden
```

---

If unauthenticated:

```text
401 Unauthorized
```

---

# @EnableWebSecurity vs Spring Boot Auto Configuration

Interview Favorite.

---

Spring Boot automatically configures security if:

```xml
spring-boot-starter-security
```

exists.

---

However:

```java
@EnableWebSecurity
```

allows full customization.

---

Most projects use:

```java
@Configuration
@EnableWebSecurity
```

for explicit configuration.

---

# Common Security Filters

Important Interview Topic.

Examples:

```text
UsernamePasswordAuthenticationFilter
BasicAuthenticationFilter
CsrfFilter
LogoutFilter
ExceptionTranslationFilter
AuthorizationFilter
```

---

Request passes through these filters.

---

# Internal Components

`@EnableWebSecurity` activates:

```text
WebSecurityConfiguration
SpringSecurityFilterChain
AuthenticationManager
AuthorizationManager
SecurityContextHolder
```

---

# Common Mistakes

## Mistake 1

Forgetting Password Encoder

Wrong:

```java
.password("123")
```

---

Use:

```java
.passwordEncoder(...)
```

or

```java
{noop}
```

for demos.

---

# Mistake 2

Disabling Security Accidentally

```java
.anyRequest().permitAll()
```

---

Removes protection.

---

# Mistake 3

Incorrect Matcher Order

Wrong:

```java
.anyRequest().authenticated()

.requestMatchers("/admin/**")
.hasRole("ADMIN")
```

---

Specific matchers must come first.

---

# Common Interview Questions

## What is @EnableWebSecurity?

Enables Spring Security's web security support.

---

## What happens internally?

Registers the security filter chain and security infrastructure.

---

## Is it mandatory in Spring Boot?

Not always.

Spring Boot auto-configures security.

---

## What replaces WebSecurityConfigurerAdapter?

```java
SecurityFilterChain
```

---

## What is SecurityFilterChain?

A chain of filters that process incoming requests.

---

## Difference Between 401 and 403?

### 401

```text
Not Authenticated
```

---

### 403

```text
Authenticated
But Not Authorized
```

---

# Enterprise Best Practice

Use:

```java
@Configuration
@EnableWebSecurity
```

with:

```java
@Bean
SecurityFilterChain
```

---

For APIs:

```java
JWT
Stateless Session
BCrypt Passwords
Role-Based Access
```

---

Avoid:

```java
WebSecurityConfigurerAdapter
```

because it is deprecated.

---

# Key Points To Remember

- `@EnableWebSecurity` activates Spring Security.
- Registers security filter chain.
- Protects web requests.
- Supports authentication and authorization.
- Commonly used with `SecurityFilterChain`.
- Enables role-based security.
- Works with JWT, OAuth2, Form Login, Basic Auth.
- Replaces older XML security configuration.
- Foundation of Spring Security.
- Frequently asked Spring Security interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@EnableWebSecurity` | Enable Web Security |
| `@EnableMethodSecurity` | Enable Method Security |
| `@PreAuthorize` | Method Authorization |
| `@Secured` | Role-Based Authorization |
| `@RolesAllowed` | JSR-250 Authorization |

---

# Interview Shortcut

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    SecurityFilterChain filterChain(
      HttpSecurity http
    ) throws Exception {

        return http.build();
    }
}
```

Meaning:

```text
Enable Spring Security
          ↓
Create Security Filter Chain
          ↓
Intercept Every Request
          ↓
Authenticate User
          ↓
Authorize User
          ↓
Allow Controller Access
```

Typical production flow:

```text
Client Request
      ↓
Security Filter Chain
      ↓
Authentication
      ↓
Authorization
      ↓
Controller
      ↓
Response
```

`@EnableWebSecurity` is the core annotation that activates Spring Security and allows you to secure web applications, REST APIs, microservices, and enterprise systems.