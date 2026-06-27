
## Definition

`@FutureOrPresent` is a Bean Validation annotation used to ensure that a date/time value is:

```text
In the Future
OR
Equal to the Current Date/Time
```

It tells the validation framework:

> The date must be greater than or equal to the current date/time.

Package:

```java
import jakarta.validation.constraints.FutureOrPresent;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.FutureOrPresent;
```

---

# Why Do We Need @FutureOrPresent?

Many business scenarios allow:

```text
Today's Date
Future Date
```

but do not allow:

```text
Past Date
```

Examples:

```text
Meeting Date
Appointment Date
Delivery Date
Booking Date
Travel Date
Event Date
Subscription Start Date
```

---

Example:

```json
{
    "appointmentDate":"2025-01-01"
}
```

Assuming today is:

```java
2026-06-27
```

This should be rejected because the appointment date is already in the past.

---

But:

```json
{
    "appointmentDate":"2026-06-27"
}
```

should be accepted.

---

Solution:

```java
@FutureOrPresent
```

---

# What Does @FutureOrPresent Validate?

Internally:

```text
Value >= Current Date/Time
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
Past Date
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
public class AppointmentRequest {

    @FutureOrPresent
    private LocalDate appointmentDate;

}
```

---

Valid:

```json
{
    "appointmentDate":"today"
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

## Does @FutureOrPresent reject null?

Answer:

```text
NO
```

---

Example:

```java
@FutureOrPresent
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

Bean Validation specification treats:

```java
null
```

as valid.

---

If value is mandatory:

```java
@NotNull

@FutureOrPresent
private LocalDate appointmentDate;
```

---

# Appointment Example

```java
@NotNull

@FutureOrPresent
private LocalDate appointmentDate;
```

---

Valid:

```java
LocalDate.now()
```

---

Valid:

```java
Tomorrow
```

---

Invalid:

```java
Yesterday
```

---

# Meeting Date Example

```java
@FutureOrPresent
private LocalDate meetingDate;
```

---

Valid:

```java
Today
```

---

Valid:

```java
2030-01-01
```

---

Invalid:

```java
2020-01-01
```

---

# Travel Date Example

```java
@FutureOrPresent
private LocalDate travelDate;
```

---

Valid:

```java
Today
```

---

Valid:

```java
Next Month
```

---

Invalid:

```java
Last Month
```

---

# LocalDate Example

```java
@FutureOrPresent
private LocalDate bookingDate;
```

---

Internally:

```java
!bookingDate.isBefore(LocalDate.now())
```

---

Equivalent:

```java
bookingDate >= today
```

---

# LocalDateTime Example

```java
@FutureOrPresent
private LocalDateTime executionTime;
```

---

Internally:

```java
!executionTime.isBefore(LocalDateTime.now())
```

---

# Instant Example

```java
@FutureOrPresent
private Instant executionTime;
```

---

Internally:

```java
!executionTime.isBefore(Instant.now())
```

---

# Custom Message

Default:

```text
must be a date in the present or in the future
```

---

Custom:

```java
@FutureOrPresent(
    message = "Appointment date cannot be in the past"
)
private LocalDate appointmentDate;
```

---

Response:

```json
{
    "message":
    "Appointment date cannot be in the past"
}
```

---

# Using @FutureOrPresent with @Valid

DTO:

```java
public class AppointmentRequest {

    @FutureOrPresent
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

@FutureOrPresent
private LocalDate appointmentDate;
```

---

Ensures:

```text
Date Exists
AND
Date Is Not In Past
```

---

# @FutureOrPresent vs @Future

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

# @FutureOrPresent vs @PastOrPresent

Interview Question.

---

## @PastOrPresent

```java
Date <= Now
```

---

Examples:

```java
Created Date
DOB
Joining Date
Transaction Date
```

---

## @FutureOrPresent

```java
Date >= Now
```

---

Examples:

```java
Appointment Date
Booking Date
Travel Date
Meeting Date
```

---

# Internal Implementation

Hibernate Validator provides validators such as:

```java
FutureOrPresentValidatorForLocalDate
```

---

```java
FutureOrPresentValidatorForLocalDateTime
```

---

```java
FutureOrPresentValidatorForInstant
```

---

```java
FutureOrPresentValidatorForDate
```

---

depending on field type.

---

# Internal Validation Logic

Example:

```java
@FutureOrPresent
private LocalDate bookingDate;
```

Internally:

```java
return !bookingDate.isBefore(LocalDate.now());
```

---

Equivalent:

```java
bookingDate >= today
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
FutureOrPresentValidator
   │
   ▼
date >= currentDate ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @FutureOrPresent to Reject Null

Wrong:

```java
@FutureOrPresent
private LocalDate bookingDate;
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

@FutureOrPresent
```

---

# Mistake 2

Using @Future Instead

Wrong:

```java
@Future
private LocalDate bookingDate;
```

---

Value:

```java
Today
```

fails validation.

---

If today should be accepted:

```java
@FutureOrPresent
```

---

# Mistake 3

Using for Historical Data

Wrong:

```java
@FutureOrPresent
private LocalDate dateOfBirth;
```

---

Use:

```java
@Past
```

or

```java
@PastOrPresent
```

---

# Common Interview Questions

## What is @FutureOrPresent?

Validates that a date/time is in the present or future.

---

## Does @FutureOrPresent allow today?

Yes.

---

## Does @FutureOrPresent allow future dates?

Yes.

---

## Does @FutureOrPresent allow past dates?

No.

---

## Does @FutureOrPresent reject null?

No.

---

## Difference Between @Future and @FutureOrPresent?

`@Future` rejects today.

`@FutureOrPresent` accepts today.

---

## Difference Between @PastOrPresent and @FutureOrPresent?

`@PastOrPresent` validates historical/current dates.

`@FutureOrPresent` validates current/future dates.

---

## Can @FutureOrPresent be used with LocalDate?

Yes.

---

## Can @FutureOrPresent be used with Instant?

Yes.

---

# Real-World Production Example

```java
public class BookingRequest {

    @NotNull(
        message = "Booking date is required"
    )

    @FutureOrPresent(
        message = "Booking date cannot be in the past"
    )
    private LocalDate bookingDate;

}
```

---

Request:

```json
{
    "bookingDate":"2020-01-01"
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "bookingDate":
    "Booking date cannot be in the past"
}
```

---

# Key Points To Remember

- `@FutureOrPresent` validates that a date/time is greater than or equal to the current date/time.
- Current date/time is allowed.
- Future dates are allowed.
- Past dates are not allowed.
- Supports Java 8 Date/Time API and legacy Date APIs.
- Does NOT reject null values.
- Use `@NotNull` if the field is mandatory.
- Commonly used for bookings, appointments, travel dates, meetings, and events.
- Uses type-specific `FutureOrPresentValidator` implementations internally.
- Often confused with `@Future`.
- Frequently asked Spring Boot and Bean Validation interview annotation.

---

# Quick Comparison

| Annotation | Past Date | Today | Future Date |
|------------|------------|---------|------------|
| `@Past` | ✅ | ❌ | ❌ |
| `@PastOrPresent` | ✅ | ✅ | ❌ |
| `@Future` | ❌ | ❌ | ✅ |
| `@FutureOrPresent` | ❌ | ✅ | ✅ |

This comparison is one of the most commonly asked Spring Boot validation interview questions.