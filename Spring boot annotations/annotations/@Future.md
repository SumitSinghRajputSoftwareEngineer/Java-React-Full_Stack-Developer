
## Definition

`@Future` is a Bean Validation annotation used to ensure that a date/time value is **strictly in the future**.

It tells the validation framework:

> The date must be after the current date/time.

Package:

```java
import jakarta.validation.constraints.Future;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.Future;
```

---

# Why Do We Need @Future?

Many business scenarios require dates that must occur in the future.

Examples:

```text
Appointment Date
Meeting Date
Flight Booking Date
Hotel Check-In Date
Event Date
Subscription Expiry Date
Loan EMI Date
Delivery Date
```

---

Example:

```json
{
    "appointmentDate":"2024-01-01"
}
```

Assuming today is:

```java
2026-06-27
```

This should be rejected because the appointment date is already in the past.

---

Solution:

```java
@Future
```

---

# What Does @Future Validate?

Internally:

```text
Value > Current Date/Time
```

---

Valid:

```java
Tomorrow
```

---

Valid:

```java
2050-01-01
```

---

Invalid:

```java
Yesterday
```

---

Invalid:

```java
Today
```

---

Invalid:

```java
Current Time
```

(Current moment is NOT considered future)

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
public class AppointmentRequest {

    @Future
    private LocalDate appointmentDate;

}
```

---

Valid:

```json
{
    "appointmentDate":"2050-01-01"
}
```

---

Invalid:

```json
{
    "appointmentDate":"2020-01-01"
}
```

---

# Important Interview Question

## Does @Future reject null?

Answer:

```text
NO
```

---

Example:

```java
@Future
private LocalDate appointmentDate;
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

@Future
private LocalDate appointmentDate;
```

---

# Appointment Date Example

```java
@NotNull

@Future
private LocalDate appointmentDate;
```

---

Valid:

```java
Tomorrow
```

---

Valid:

```java
2050-01-01
```

---

Invalid:

```java
Today
```

---

Invalid:

```java
Yesterday
```

---

# Flight Booking Example

```java
@Future
private LocalDate departureDate;
```

---

Valid:

```java
2030-05-01
```

---

Invalid:

```java
2025-05-01
```

---

# Event Date Example

```java
@Future
private LocalDate eventDate;
```

---

Valid:

```java
2027-12-31
```

---

Invalid:

```java
2026-01-01
```

(if current date is later)

---

# Expiry Date Example

```java
@Future
private LocalDate expiryDate;
```

---

Valid:

```java
2035-12-31
```

---

Invalid:

```java
2024-12-31
```

---

# LocalDate Example

```java
@Future
private LocalDate appointmentDate;
```

---

Internally:

```java
appointmentDate.isAfter(LocalDate.now())
```

---

# LocalDateTime Example

```java
@Future
private LocalDateTime meetingTime;
```

---

Internally:

```java
meetingTime.isAfter(LocalDateTime.now())
```

---

# Instant Example

```java
@Future
private Instant executionTime;
```

---

Internally:

```java
executionTime.isAfter(Instant.now())
```

---

# Custom Message

Default:

```text
must be a future date
```

---

Custom:

```java
@Future(
    message = "Appointment date must be in the future"
)
private LocalDate appointmentDate;
```

---

Response:

```json
{
    "message":
    "Appointment date must be in the future"
}
```

---

# Using @Future with @Valid

DTO:

```java
public class AppointmentRequest {

    @Future
    private LocalDate appointmentDate;

}
```

---

Controller:

```java
@PostMapping("/appointments")
public String create(

        @Valid
        @RequestBody
        AppointmentRequest request) {

    return "saved";
}
```

---

Request:

```json
{
    "appointmentDate":"2020-01-01"
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

@Future
private LocalDate appointmentDate;
```

---

Ensures:

```text
Date Exists
AND
Date Is In Future
```

---

# @Future vs @FutureOrPresent

Most Asked Interview Question.

---

## @Future

Checks:

```text
Date > Current Date/Time
```

---

Valid:

```java
Tomorrow
```

---

Invalid:

```java
Today
```

---

## @FutureOrPresent

Checks:

```text
Date >= Current Date/Time
```

---

Valid:

```java
Today
```

---

Valid:

```java
Tomorrow
```

---

Comparison:

| Date | @Future | @FutureOrPresent |
|--------|----------|------------------|
| Yesterday | ❌ | ❌ |
| Today | ❌ | ✅ |
| Tomorrow | ✅ | ✅ |

---

# @Future vs @Past

Interview Question.

---

## @Future

```java
Date > Now
```

---

Examples:

```java
Appointment
Meeting
Booking
Delivery
Expiry
```

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
Graduation Date
Manufacturing Date
```

---

# Internal Implementation

Hibernate Validator provides validators such as:

```java
FutureValidatorForLocalDate
```

---

```java
FutureValidatorForLocalDateTime
```

---

```java
FutureValidatorForInstant
```

---

```java
FutureValidatorForDate
```

---

depending on field type.

---

# Internal Validation Logic

Example:

```java
@Future
private LocalDate appointmentDate;
```

Internally:

```java
return appointmentDate.isAfter(LocalDate.now());
```

---

Equivalent:

```java
appointmentDate > today
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
FutureValidator
   │
   ▼
date > currentDate ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @Future to Reject Null

Wrong:

```java
@Future
private LocalDate appointmentDate;
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

@Future
```

---

# Mistake 2

Expecting Today to Pass

Wrong assumption:

```java
@Future
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
@FutureOrPresent
```

if today should be accepted.

---

# Mistake 3

Using @Future for DOB

Wrong:

```java
@Future
private LocalDate dateOfBirth;
```

---

Use:

```java
@Past
```

---

# Common Interview Questions

## What is @Future?

Validates that a date/time is strictly after the current date/time.

---

## Does @Future allow today?

No.

---

## Does @Future allow future dates?

Yes.

---

## Does @Future reject past dates?

Yes.

---

## Does @Future reject null?

No.

---

## Difference Between @Future and @FutureOrPresent?

`@Future` rejects current date/time.

`@FutureOrPresent` accepts current date/time.

---

## Difference Between @Future and @Past?

`@Future` validates upcoming dates.

`@Past` validates historical dates.

---

## Can @Future be used with LocalDate?

Yes.

---

## Can @Future be used with Instant?

Yes.

---

# Real-World Production Example

```java
public class AppointmentRequest {

    @NotNull(
        message = "Appointment date is required"
    )

    @Future(
        message = "Appointment date must be in the future"
    )
    private LocalDate appointmentDate;

}
```

---

Request:

```json
{
    "appointmentDate":"2020-01-01"
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "appointmentDate":
    "Appointment date must be in the future"
}
```

---

# Key Points To Remember

- `@Future` validates that a date/time is strictly after the current date/time.
- Current date/time is NOT allowed.
- Future dates are allowed.
- Past dates are not allowed.
- Supports Java 8 Date/Time API and legacy Date APIs.
- Does NOT reject null values.
- Use `@NotNull` if the value is mandatory.
- Commonly used for appointments, bookings, deliveries, events, and expiry dates.
- Uses type-specific `FutureValidator` implementations internally.
- Often confused with `@FutureOrPresent`.
- Frequently asked Spring Boot and Bean Validation interview annotation.

---

# Quick Comparison

| Annotation | Past Date | Today | Future Date |
|------------|------------|---------|------------|
| `@Past` | ✅ | ❌ | ❌ |
| `@PastOrPresent` | ✅ | ✅ | ❌ |
| `@Future` | ❌ | ❌ | ✅ |
| `@FutureOrPresent` | ❌ | ✅ | ✅ |

This comparison is one of the most frequently asked Spring Boot validation interview questions.