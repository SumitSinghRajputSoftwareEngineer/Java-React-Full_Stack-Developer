
## Definition

`@PastOrPresent` is a Bean Validation annotation used to ensure that a date/time value is:

```text
In the Past
OR
Equal to the Current Date/Time
```

It tells the validation framework:

> The date must be less than or equal to the current date/time.

Package:

```java
import jakarta.validation.constraints.PastOrPresent;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.PastOrPresent;
```

---

# Why Do We Need @PastOrPresent?

Many business fields can contain:

```text
Past Dates
Current Date
```

but should never contain:

```text
Future Dates
```

Examples:

```text
Date of Birth
Joining Date
Manufacturing Date
Created Date
Last Login Date
Transaction Date
Order Date
```

---

Example:

```json
{
    "createdDate":"2035-01-01"
}
```

should be rejected.

---

But:

```json
{
    "createdDate":"today"
}
```

should be accepted.

---

Solution:

```java
@PastOrPresent
```

---

# What Does @PastOrPresent Validate?

Internally:

```text
Value <= Current Date/Time
```

---

Valid:

```java
Yesterday
```

---

Valid:

```java
Today
```

---

Valid:

```java
Current Time
```

---

Invalid:

```java
Tomorrow
```

---

Invalid:

```java
Future Date
```

---

# Supported Types

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

    @PastOrPresent
    private LocalDate joiningDate;

}
```

---

Valid:

```json
{
    "joiningDate":"2020-01-01"
}
```

---

Valid:

```json
{
    "joiningDate":"today"
}
```

---

Invalid:

```json
{
    "joiningDate":"2050-01-01"
}
```

---

# Important Interview Question

Does @PastOrPresent reject null?

Answer:

```text
NO
```

---

Example:

```java
@PastOrPresent
private LocalDate joiningDate;
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

If value is mandatory:

```java
@NotNull

@PastOrPresent
private LocalDate joiningDate;
```

---

# Date of Birth Example

```java
@NotNull

@PastOrPresent
private LocalDate dateOfBirth;
```

---

Valid:

```java
1995-10-10
```

---

Valid:

```java
LocalDate.now()
```

---

Invalid:

```java
2050-10-10
```

---

# Created Date Example

```java
@PastOrPresent
private LocalDate createdDate;
```

---

Valid:

```java
2025-01-01
```

---

Valid:

```java
Today
```

---

Invalid:

```java
Tomorrow
```

---

# Transaction Date Example

```java
@PastOrPresent
private LocalDate transactionDate;
```

---

Valid:

```java
Yesterday
```

---

Valid:

```java
Today
```

---

Invalid:

```java
Future Date
```

---

# LocalDate Example

```java
@PastOrPresent
private LocalDate date;
```

---

Internally:

```java
!date.isAfter(LocalDate.now())
```

---

Equivalent:

```java
date <= today
```

---

# LocalDateTime Example

```java
@PastOrPresent
private LocalDateTime createdAt;
```

---

Internally:

```java
!createdAt.isAfter(LocalDateTime.now())
```

---

# Instant Example

```java
@PastOrPresent
private Instant createdAt;
```

---

Internally:

```java
!createdAt.isAfter(Instant.now())
```

---

# Custom Message

Default:

```text
must be a date in the past or in the present
```

---

Custom:

```java
@PastOrPresent(
    message = "Joining date cannot be in the future"
)
private LocalDate joiningDate;
```

---

Response:

```json
{
    "message":
    "Joining date cannot be in the future"
}
```

---

# Using @PastOrPresent with @Valid

DTO:

```java
public class EmployeeRequest {

    @PastOrPresent
    private LocalDate joiningDate;

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
    "joiningDate":"2050-01-01"
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Combining with Other Validations

Very common.

---

```java
@NotNull

@PastOrPresent
private LocalDate joiningDate;
```

---

Ensures:

```text
Date Exists
AND
Date Is Not In Future
```

---

# @PastOrPresent vs @Past

Most Asked Interview Question.

---

## @Past

Checks:

```text
Date < Current Date/Time
```

---

Valid:

```java
Yesterday
```

---

Invalid:

```java
Today
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
Yesterday
```

---

Valid:

```java
Today
```

---

Comparison:

| Date | @Past | @PastOrPresent |
|--------|--------|---------------|
| Yesterday | ✅ | ✅ |
| Today | ❌ | ✅ |
| Tomorrow | ❌ | ❌ |

---

# @PastOrPresent vs @Future

Interview Question.

---

## @PastOrPresent

```java
Date <= Now
```

---

Examples:

```java
DOB
Joining Date
Created Date
Transaction Date
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
Meeting Date
Expiry Date
```

---

# Internal Implementation

Hibernate Validator provides validators such as:

```java
PastOrPresentValidatorForLocalDate
```

---

```java
PastOrPresentValidatorForLocalDateTime
```

---

```java
PastOrPresentValidatorForInstant
```

---

```java
PastOrPresentValidatorForDate
```

---

depending on field type.

---

# Internal Validation Logic

Example:

```java
@PastOrPresent
private LocalDate joiningDate;
```

Internally:

```java
return !joiningDate.isAfter(LocalDate.now());
```

---

Equivalent:

```java
joiningDate <= today
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
PastOrPresentValidator
   │
   ▼
date <= currentDate ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @PastOrPresent to Reject Null

Wrong:

```java
@PastOrPresent
private LocalDate joiningDate;
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

@PastOrPresent
```

---

# Mistake 2

Using @Past Instead

Wrong:

```java
@Past
private LocalDate joiningDate;
```

---

Value:

```java
Today
```

fails validation.

---

If today should be allowed:

```java
@PastOrPresent
```

---

# Mistake 3

Using for Future Events

Wrong:

```java
@PastOrPresent
private LocalDate appointmentDate;
```

---

Use:

```java
@Future
```

for appointments.

---

# Common Interview Questions

## What is @PastOrPresent?

Validates that a date/time is in the past or present.

---

## Does @PastOrPresent allow today?

Yes.

---

## Does @PastOrPresent allow future dates?

No.

---

## Does @PastOrPresent reject null?

No.

---

## Difference Between @Past and @PastOrPresent?

`@Past` rejects current date/time.

`@PastOrPresent` accepts current date/time.

---

## Difference Between @PastOrPresent and @Future?

`@PastOrPresent` validates historical/current dates.

`@Future` validates upcoming dates.

---

## Can @PastOrPresent be used with LocalDate?

Yes.

---

## Can @PastOrPresent be used with Instant?

Yes.

---

# Real-World Production Example

```java
public class EmployeeRequest {

    @NotNull(
        message = "Joining date is required"
    )

    @PastOrPresent(
        message = "Joining date cannot be in the future"
    )
    private LocalDate joiningDate;

}
```

---

Request:

```json
{
    "joiningDate":"2050-01-01"
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "joiningDate":
    "Joining date cannot be in the future"
}
```

---

# Key Points To Remember

- `@PastOrPresent` validates that a date/time is less than or equal to the current date/time.
- Current date/time is allowed.
- Future dates are not allowed.
- Supports Java 8 Date/Time API and legacy Date APIs.
- Does NOT reject null values.
- Use `@NotNull` if the field is mandatory.
- Commonly used for joining dates, created dates, transaction dates, and DOB validations.
- Uses type-specific `PastOrPresentValidator` implementations internally.
- Often confused with `@Past`.
- Frequently asked Spring Boot and Bean Validation interview annotation.

---

# Quick Comparison

| Annotation | Past Date | Today | Future Date |
|------------|------------|---------|------------|
| `@Past` | ✅ | ❌ | ❌ |
| `@PastOrPresent` | ✅ | ✅ | ❌ |
| `@Future` | ❌ | ❌ | ✅ |
| `@FutureOrPresent` | ❌ | ✅ | ✅ |

This comparison is one of the most common Spring Boot interview questions.