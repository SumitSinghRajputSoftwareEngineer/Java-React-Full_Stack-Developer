
## Definition

`@Past` is a Bean Validation annotation used to ensure that a date/time value is **strictly in the past**.

It tells the validation framework:

> The date must be before the current date/time.

Package:

```java
import jakarta.validation.constraints.Past;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.Past;
```

---

# Why Do We Need @Past?

Many business scenarios require dates that must already have occurred.

Examples:

```text
Date of Birth
Joining Date
Manufacturing Date
Previous Employment Date
Graduation Date
```

---

Example:

```json
{
    "dateOfBirth":"2030-01-01"
}
```

should be rejected because a person cannot be born in the future.

---

Solution:

```java
@Past
```

---

# What Does @Past Validate?

Internally:

```text
Value < Current Date/Time
```

---

Valid:

```java
2000-01-01
```

---

Valid:

```java
1995-10-20
```

---

Invalid:

```java
2030-01-01
```

---

Invalid:

```java
Tomorrow
```

---

Invalid:

```java
Current Date/Time
```

(Important: current moment is NOT considered past)

---

# Supported Types

`@Past` supports:

## Java 8 Date/Time API

```java
LocalDate
LocalDateTime
LocalTime
OffsetDateTime
OffsetTime
ZonedDateTime
Year
YearMonth
Instant
```

---

## Legacy Date API

```java
java.util.Date
java.util.Calendar
```

---

# Basic Example

```java
public class EmployeeRequest {

    @Past
    private LocalDate dateOfBirth;

}
```

---

Valid:

```json
{
    "dateOfBirth":"1998-05-10"
}
```

---

Invalid:

```json
{
    "dateOfBirth":"2050-05-10"
}
```

---

# Important Interview Question

Does @Past reject null?

Answer:

```text
NO
```

---

Example:

```java
@Past
private LocalDate dob;
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

Bean Validation treats:

```java
null
```

as valid.

---

If date is mandatory:

```java
@NotNull

@Past
private LocalDate dob;
```

---

# Date of Birth Example

```java
@NotNull

@Past
private LocalDate dateOfBirth;
```

---

Valid:

```java
1995-01-10
```

---

Invalid:

```java
2030-01-10
```

---

Invalid:

```java
null
```

because of:

```java
@NotNull
```

---

# Employee Joining Date Example

```java
@Past
private LocalDate joiningDate;
```

---

Valid:

```java
2020-01-01
```

---

Invalid:

```java
2050-01-01
```

---

# Manufacturing Date Example

```java
@Past
private LocalDate manufacturingDate;
```

---

Valid:

```java
2024-01-01
```

---

Invalid:

```java
2035-01-01
```

---

# LocalDate Example

```java
@Past
private LocalDate birthDate;
```

---

Checks:

```java
birthDate.isBefore(LocalDate.now())
```

---

# LocalDateTime Example

```java
@Past
private LocalDateTime createdAt;
```

---

Checks:

```java
createdAt.isBefore(LocalDateTime.now())
```

---

# Instant Example

```java
@Past
private Instant createdAt;
```

---

Checks:

```java
createdAt.isBefore(Instant.now())
```

---

# Custom Message

Default:

```text
must be a past date
```

---

Custom:

```java
@Past(
    message = "Date of birth must be in the past"
)
private LocalDate dateOfBirth;
```

---

Response:

```json
{
    "message":
    "Date of birth must be in the past"
}
```

---

# Using @Past with @Valid

DTO:

```java
public class EmployeeRequest {

    @Past
    private LocalDate dob;

}
```

---

Controller:

```java
@PostMapping("/employees")
public String save(

        @Valid
        @RequestBody
        EmployeeRequest request) {

    return "saved";
}
```

---

Request:

```json
{
    "dob":"2050-01-01"
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Combining with Other Validations

Common production usage:

```java
@NotNull

@Past
private LocalDate dateOfBirth;
```

---

Ensures:

```text
Date Exists
AND
Date Is In Past
```

---

# @Past vs @PastOrPresent

Most Asked Interview Question.

---

## @Past

Checks:

```text
Date < Current Date/Time
```

---

Current date:

```java
2026-06-27
```

---

Valid:

```java
2026-06-26
```

---

Invalid:

```java
2026-06-27
```

---

## @PastOrPresent

Checks:

```text
Date <= Current Date/Time
```

---

Valid:

```java
2026-06-26
```

---

Valid:

```java
2026-06-27
```

---

Comparison:

| Date | @Past | @PastOrPresent |
|--------|--------|---------------|
| Yesterday | ✅ | ✅ |
| Today | ❌ | ✅ |
| Tomorrow | ❌ | ❌ |

---

# @Past vs @Future

Interview Question.

---

## @Past

```java
Date < Now
```

---

Examples:

```java
DOB
Joining Date
Manufacturing Date
```

---

## @Future

```java
Date > Now
```

---

Examples:

```java
Appointment Date
Booking Date
Expiry Date
Event Date
```

---

# Internal Implementation

Hibernate Validator provides validators such as:

```java
PastValidatorForLocalDate
```

---

```java
PastValidatorForLocalDateTime
```

---

```java
PastValidatorForInstant
```

---

```java
PastValidatorForDate
```

---

depending on field type.

---

# Internal Validation Logic

Example:

```java
@Past
private LocalDate dob;
```

Internally:

```java
return dob.isBefore(LocalDate.now());
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
PastValidator
   │
   ▼
date < currentDate ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @Past to Reject Null

Wrong:

```java
@Past
private LocalDate dob;
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
@NotNull

@Past
```

---

# Mistake 2

Using @Past for Future Events

Wrong:

```java
@Past
private LocalDate appointmentDate;
```

---

Appointment should be:

```java
@Future
```

---

# Mistake 3

Expecting Today to Pass

Wrong assumption:

```java
@Past
```

allows today.

---

Actually:

```text
Today = FAIL
```

---

Use:

```java
@PastOrPresent
```

if today should be allowed.

---

# Common Interview Questions

## What is @Past?

Validates that a date/time value is strictly before the current date/time.

---

## Does @Past allow today?

No.

---

## Does @Past reject future dates?

Yes.

---

## Does @Past reject null?

No.

---

## How do we reject null values?

```java
@NotNull

@Past
```

---

## Difference Between @Past and @PastOrPresent?

`@Past` rejects current date/time.

`@PastOrPresent` accepts current date/time.

---

## Difference Between @Past and @Future?

`@Past` validates historical dates.

`@Future` validates upcoming dates.

---

# Real-World Production Example

```java
public class EmployeeRequest {

    @NotNull(
        message = "Date of birth is required"
    )

    @Past(
        message = "Date of birth must be in the past"
    )
    private LocalDate dateOfBirth;

}
```

---

Request:

```json
{
    "dateOfBirth":"2035-01-01"
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "dateOfBirth":
    "Date of birth must be in the past"
}
```

---

# Key Points To Remember

- `@Past` validates that a date/time is strictly before the current date/time.
- Current date/time is NOT allowed.
- Future dates are NOT allowed.
- Supports Java 8 Date/Time API and legacy Date APIs.
- Does NOT reject null values.
- Use `@NotNull` if the value is mandatory.
- Commonly used for DOB, joining dates, graduation dates, and manufacturing dates.
- Uses type-specific `PastValidator` implementations internally.
- Often confused with `@PastOrPresent`.
- Frequently asked Spring Boot and Bean Validation interview annotation.