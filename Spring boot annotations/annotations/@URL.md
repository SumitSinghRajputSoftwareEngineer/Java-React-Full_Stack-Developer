
## Definition

`@URL` is a **Hibernate Validator-specific** annotation used to validate whether a String contains a valid URL.

It tells the validation framework:

> The field must contain a properly formatted URL.

Package:

```java
import org.hibernate.validator.constraints.URL;
```

---

# Important Interview Point

`@URL` is:

```text
NOT a Jakarta Bean Validation Standard Annotation
```

It is provided by:

```text
Hibernate Validator
```

---

Unlike:

```java
@NotNull
@Email
@Size
@Pattern
```

which are standard Bean Validation annotations.

---

# Why Do We Need @URL?

Many applications accept URLs from users.

Examples:

```text
Website URL
LinkedIn Profile
GitHub Profile
Portfolio Website
Company Website
Image URL
Video URL
Webhook URL
```

---

Example:

Business Rule:

```text
User website must be a valid URL.
```

---

Valid:

```text
https://www.google.com
```

---

Valid:

```text
https://github.com/sumit
```

---

Invalid:

```text
google
```

---

Invalid:

```text
www.google
```

---

Solution:

```java
@URL
```

---

# Basic Syntax

```java
@URL
private String website;
```

---

Meaning:

```text
website must contain a valid URL
```

---

# Basic Example

```java
public class UserProfileRequest {

    @URL
    private String website;

}
```

---

Valid:

```java
"https://www.google.com"
```

---

Valid:

```java
"https://github.com"
```

---

Invalid:

```java
"google"
```

---

Invalid:

```java
"abc"
```

---

# Important Interview Question

## Does @URL reject null?

Answer:

```text
NO
```

---

Example:

```java
@URL
private String website;
```

---

Value:

```java
null
```

Validation:

```text
PASS
```

---

Why?

Most Bean Validation constraints treat:

```java
null
```

as valid.

---

If URL is mandatory:

```java
@NotBlank

@URL
private String website;
```

---

# Website Example

```java
@URL(
    message =
    "Please provide a valid website URL"
)
private String website;
```

---

Valid:

```java
"https://example.com"
```

---

Valid:

```java
"http://example.com"
```

---

Invalid:

```java
"example.com"
```

---

Invalid:

```java
"website"
```

---

# GitHub Profile Example

```java
@URL
private String githubProfile;
```

---

Valid:

```java
"https://github.com/sumit"
```

---

Invalid:

```java
"github.com/sumit"
```

(Depending on validator configuration, protocol may be required.)

---

# LinkedIn Example

```java
@URL
private String linkedinProfile;
```

---

Valid:

```java
"https://www.linkedin.com/in/sumit"
```

---

Invalid:

```java
"linkedin-profile"
```

---

# Custom Message

```java
@URL(
    message =
    "Invalid URL format"
)
private String website;
```

---

Response:

```json
{
    "website":
    "Invalid URL format"
}
```

---

# Using @URL with @Valid

DTO:

```java
public class ProfileRequest {

    @URL
    private String website;

}
```

---

Controller:

```java
@PostMapping("/profile")
public String create(

        @Valid
        @RequestBody
        ProfileRequest request) {

    return "saved";
}
```

---

Request:

```json
{
   "website":"abc"
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Combining with Other Validations

Common usage:

```java
@NotBlank

@URL
private String website;
```

---

Ensures:

```text
Not Null
Not Empty
Not Blank
Valid URL Format
```

---

# Protocol Restriction

You can restrict allowed protocols.

---

Example:

```java
@URL(
    protocol = "https"
)
private String website;
```

---

Valid:

```java
https://google.com
```

---

Invalid:

```java
http://google.com
```

---

# Host Restriction

Example:

```java
@URL(
    host = "github.com"
)
private String githubProfile;
```

---

Valid:

```java
https://github.com/sumit
```

---

Invalid:

```java
https://google.com
```

---

# Port Restriction

Example:

```java
@URL(
    port = 8080
)
private String serviceUrl;
```

---

Valid:

```java
http://localhost:8080
```

---

Invalid:

```java
http://localhost:9090
```

---

# @URL vs @Pattern

Most Asked Interview Question.

---

## @URL

Purpose:

```text
Validate URL Structure
```

---

Example:

```java
@URL
private String website;
```

---

## @Pattern

Purpose:

```text
Validate Custom Regex
```

---

Example:

```java
@Pattern(
    regexp =
    "^https://.*$"
)
private String website;
```

---

Comparison:

| Feature | @URL | @Pattern |
|----------|-------|----------|
| URL Validation | ✅ | Manual |
| Regex Required | ❌ | ✅ |
| Simpler | ✅ | ❌ |

---

# @URL vs @Email

Interview Question.

---

## @Email

Validates:

```text
Email Address
```

---

Example:

```java
user@gmail.com
```

---

## @URL

Validates:

```text
Website Address
```

---

Example:

```java
https://google.com
```

---

Comparison:

| Annotation | Validates |
|------------|------------|
| @Email | Email Address |
| @URL | Website URL |

---

# Internal Implementation

Hibernate Validator uses:

```java
URLValidator
```

---

Package:

```java
org.hibernate.validator.internal.constraintvalidators
```

---

Internally it uses:

```java
java.net.URL
```

and URL parsing logic.

---

# Internal Validation Logic

Simplified:

```java
if(value == null)
    return true;

try {
    new URL(value);
    return true;
}
catch(Exception e) {
    return false;
}
```

---

# Internal Architecture

```text
Request
   │
   ▼
DTO Creation
   │
   ▼
@Valid / @Validated
   │
   ▼
Hibernate Validator
   │
   ▼
URLValidator
   │
   ▼
Parse URL
   │
 ┌─Valid────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @URL to Reject Null

Wrong:

```java
@URL
private String website;
```

---

Value:

```java
null
```

passes validation.

---

Use:

```java
@NotBlank

@URL
```

---

# Mistake 2

Confusing URL with Email

Wrong:

```java
@URL
private String email;
```

---

Use:

```java
@Email
```

---

# Mistake 3

Thinking @URL Is Standard Bean Validation

Wrong assumption:

```java
@URL
```

belongs to Jakarta Validation.

---

Actually:

```java
org.hibernate.validator.constraints.URL
```

is Hibernate-specific.

---

# Common Interview Questions

## What is @URL?

Validates whether a String contains a valid URL.

---

## Is @URL part of Jakarta Bean Validation?

No.

It is Hibernate-specific.

---

## Does @URL reject null?

No.

---

## How do you make URL mandatory?

```java
@NotBlank

@URL
```

---

## Difference Between @URL and @Pattern?

`@URL`

```text
Built-in URL validation
```

---

`@Pattern`

```text
Custom regex validation
```

---

## Difference Between @URL and @Email?

`@Email`

```text
Validates email address
```

---

`@URL`

```text
Validates website URL
```

---

# Real-World Production Example

```java
public class ProfileRequest {

    @NotBlank(
        message =
        "Website URL is required"
    )

    @URL(
        protocol = "https",
        message =
        "Only HTTPS URLs are allowed"
    )
    private String website;

}
```

---

Request:

```json
{
   "website":"http://google.com"
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
   "website":
   "Only HTTPS URLs are allowed"
}
```

---

# Key Points To Remember

- `@URL` validates URL format.
- Hibernate Validator-specific annotation.
- Not part of Jakarta Bean Validation specification.
- Works only on String fields.
- Does NOT reject null values.
- Combine with `@NotBlank` if mandatory.
- Supports protocol restriction.
- Supports host restriction.
- Supports port restriction.
- Frequently used for website, GitHub, LinkedIn, portfolio, and webhook URLs.
- Internally uses `URLValidator`.
- Frequently asked Spring Boot and Hibernate Validator interview annotation.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@URL` | URL Validation |
| `@Email` | Email Validation |
| `@Pattern` | Regex Validation |
| `@NotBlank` | Mandatory Text |
| `@Size` | Length Validation |

---

# Interview Shortcut

### Hibernate Specific

```java
@URL
private String website;
```

### Production Version

```java
@NotBlank

@URL(
    protocol = "https"
)
private String website;
```

Meaning:

```text
Website is required
AND
Must be a valid HTTPS URL
```

This is the most common real-world Spring Boot usage of `@URL`.