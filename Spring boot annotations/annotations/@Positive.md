
## Definition

`@Positive` is a Bean Validation annotation used to ensure that a numeric value is **strictly greater than zero**.

It tells the validation framework:

> The value must be > 0.

Package:

```java
import jakarta.validation.constraints.Positive;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.Positive;
```

---

# Why Do We Need @Positive?

Suppose your application contains:

```text
Order Quantity
Employee ID
Price
Amount
Transaction Value
```

These values should never be:

```text
0
Negative
```

---

Example:

```json
{
    "quantity": -5
}
```

or

```json
{
    "amount": 0
}
```

should be rejected.

---

Solution:

```java
@Positive
```

---

# What Does @Positive Validate?

Internally:

```java
value > 0
```

---

Valid:

```java
1
10
100
0.5
9999
```

---

Invalid:

```java
0
-1
-100
```

---

# Supported Types

`@Positive` supports:

```java
byte
short
int
long
float
double
```

Wrapper types:

```java
Byte
Short
Integer
Long
Float
Double
```

Also:

```java
BigInteger
BigDecimal
```

---

# Basic Example

```java
public class OrderRequest {

    @Positive
    private Integer quantity;

}
```

---

Valid:

```json
{
    "quantity":1
}
```

---

Valid:

```json
{
    "quantity":10
}
```

---

Invalid:

```json
{
    "quantity":0
}
```

---

Invalid:

```json
{
    "quantity":-5
}
```

---

# Important Interview Question

Does @Positive reject null?

Answer:

```text
NO
```

---

Example:

```java
@Positive
private Integer quantity;
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

If null should fail:

```java
@NotNull
@Positive
private Integer quantity;
```

---

# Quantity Validation Example

```java
@NotNull

@Positive
private Integer quantity;
```

---

Valid:

```java
1
5
100
```

---

Invalid:

```java
0
```

---

Invalid:

```java
-10
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

# Employee ID Validation

```java
@Positive
private Long employeeId;
```

---

Valid:

```java
1
100
5000
```

---

Invalid:

```java
0
-1
```

---

# Price Validation Example

```java
@Positive
private BigDecimal price;
```

---

Valid:

```java
99.99
```

---

Valid:

```java
0.01
```

---

Invalid:

```java
0
```

---

Invalid:

```java
-99.99
```

---

# Salary Validation Example

```java
@Positive
private Double salary;
```

---

Valid:

```java
50000.0
```

---

Invalid:

```java
0.0
```

---

Invalid:

```java
-50000.0
```

---

# Custom Message

Default:

```text
must be greater than 0
```

---

Custom:

```java
@Positive(
    message = "Quantity must be greater than zero"
)
private Integer quantity;
```

---

Error:

```json
{
    "message":
    "Quantity must be greater than zero"
}
```

---

# Using @Positive with @Valid

DTO:

```java
public class OrderRequest {

    @Positive
    private Integer quantity;

}
```

---

Controller:

```java
@PostMapping("/orders")
public String create(

        @Valid
        @RequestBody
        OrderRequest request) {

    return "saved";
}
```

---

Request:

```json
{
    "quantity":0
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Method Parameter Validation

Requires:

```java
@Validated
```

---

Example:

```java
@RestController

@Validated
public class EmployeeController {

    @GetMapping("/{id}")

    public String find(

            @Positive
            @PathVariable
            Long id) {

        return "employee";
    }

}
```

---

Valid:

```http
/employees/100
```

---

Invalid:

```http
/employees/0
```

---

Invalid:

```http
/employees/-10
```

---

Throws:

```java
ConstraintViolationException
```

---

# Service Layer Validation

```java
@Service

@Validated
public class PaymentService {

    public void process(

            @Positive
            Double amount) {

    }

}
```

---

Call:

```java
process(0.0);
```

---

Result:

```java
ConstraintViolationException
```

---

# @Positive vs @Min(1)

Most Frequently Asked Interview Question.

---

## @Positive

Checks:

```java
value > 0
```

---

Example:

```java
@Positive
private Integer quantity;
```

---

Valid:

```java
1
10
100
```

---

Invalid:

```java
0
-1
```

---

## @Min(1)

Checks:

```java
value >= 1
```

---

Example:

```java
@Min(1)
private Integer quantity;
```

---

Valid:

```java
1
10
100
```

---

Invalid:

```java
0
-1
```

---

For integers:

```java
@Positive
```

and

```java
@Min(1)
```

behave similarly.

---

# Key Difference

`@Positive` works naturally with:

```java
BigDecimal
Double
Float
```

---

Example:

```java
@Positive
private BigDecimal amount;
```

---

Valid:

```java
0.01
```

---

Equivalent using @Min:

```java
@Min(1)
```

would fail.

---

Therefore:

```java
@Positive
```

is more suitable for decimal values.

---

# @Positive vs @PositiveOrZero

Very Important.

---

## @Positive

Checks:

```java
value > 0
```

---

Invalid:

```java
0
```

---

## @PositiveOrZero

Checks:

```java
value >= 0
```

---

Valid:

```java
0
```

---

Comparison:

| Value | @Positive | @PositiveOrZero |
|---------|-----------|----------------|
| -1 | ❌ | ❌ |
| 0 | ❌ | ✅ |
| 1 | ✅ | ✅ |

---

# Internal Implementation

Hibernate Validator provides:

```java
PositiveValidatorForInteger
```

---

```java
PositiveValidatorForLong
```

---

```java
PositiveValidatorForDouble
```

---

```java
PositiveValidatorForBigDecimal
```

---

depending on type.

---

# Internal Validation Logic

Example:

```java
@Positive
private Integer quantity;
```

Internally:

```java
return value > 0;
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
PositiveValidator
   │
   ▼
value > 0 ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @Positive to Reject Null

Wrong assumption:

```java
@Positive
private Integer quantity;
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
@Positive
```

---

# Mistake 2

Using @Positive for Zero Values

Wrong:

```java
@Positive
private Integer stock;
```

---

Value:

```java
0
```

fails validation.

---

Use:

```java
@PositiveOrZero
```

---

# Mistake 3

Using @Positive on String

Wrong:

```java
@Positive
private String quantity;
```

---

Not supported.

---

Use:

```java
@Pattern
```

or numeric types.

---

# Common Interview Questions

## What is @Positive?

Validates that a numeric value is strictly greater than zero.

---

## Does @Positive allow zero?

No.

---

## Does @Positive reject negative numbers?

Yes.

---

## Does @Positive reject null?

No.

---

## How do we reject null?

```java
@NotNull
@Positive
```

---

## Difference Between @Positive and @Min(1)?

For integers they are similar.

`@Positive` works better with decimal values.

---

## Difference Between @Positive and @PositiveOrZero?

`@Positive` rejects zero.

`@PositiveOrZero` accepts zero.

---

# Real-World Production Example

```java
public class PaymentRequest {

    @NotNull

    @Positive(
        message = "Amount must be greater than zero"
    )
    private BigDecimal amount;

    @NotNull

    @Positive(
        message = "Quantity must be greater than zero"
    )
    private Integer quantity;

}
```

---

Request:

```json
{
    "amount":0,
    "quantity":-5
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "amount":"Amount must be greater than zero",
    "quantity":"Quantity must be greater than zero"
}
```

---

# Key Points To Remember

- `@Positive` validates that a numeric value is strictly greater than zero.
- Zero is NOT allowed.
- Negative values are NOT allowed.
- Supports integer and decimal numeric types.
- Does NOT reject null values.
- Use `@NotNull` if null should fail validation.
- Commonly used for IDs, quantities, prices, salaries, and transaction amounts.
- Works better than `@Min(1)` for decimal values.
- Uses type-specific `PositiveValidator` implementations internally.
- Frequently asked Spring Boot, Hibernate Validator, and interview annotation.