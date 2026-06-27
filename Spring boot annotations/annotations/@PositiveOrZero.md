
## Definition

`@PositiveOrZero` is a Bean Validation annotation used to ensure that a numeric value is:

```text
Greater than zero
OR
Equal to zero
```

It tells the validation framework:

> The value must be >= 0.

Package:

```java
import jakarta.validation.constraints.PositiveOrZero;
```

Prior to Spring Boot 3:

```java
import javax.validation.constraints.PositiveOrZero;
```

---

# Why Do We Need @PositiveOrZero?

Many business scenarios allow:

```text
0
Positive Numbers
```

but do not allow:

```text
Negative Numbers
```

Examples:

```text
Product Stock
Wallet Balance
Account Balance
Reward Points
Leave Balance
Cart Quantity
```

---

Example:

```json
{
    "stock": -5
}
```

should be rejected.

---

But:

```json
{
    "stock": 0
}
```

should be valid.

---

Solution:

```java
@PositiveOrZero
```

---

# What Does @PositiveOrZero Validate?

Internally:

```java
value >= 0
```

---

Valid:

```java
0
1
10
100
999
```

---

Invalid:

```java
-1
-10
-999
```

---

# Supported Types

`@PositiveOrZero` supports:

Primitive types:

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
public class ProductRequest {

    @PositiveOrZero
    private Integer stock;

}
```

---

Valid:

```json
{
    "stock":0
}
```

---

Valid:

```json
{
    "stock":10
}
```

---

Invalid:

```json
{
    "stock":-5
}
```

---

# Important Interview Question

Does @PositiveOrZero reject null?

Answer:

```text
NO
```

---

Example:

```java
@PositiveOrZero
private Integer stock;
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
@PositiveOrZero
private Integer stock;
```

---

# Stock Validation Example

```java
@NotNull

@PositiveOrZero
private Integer stock;
```

---

Valid:

```java
0
10
500
```

---

Invalid:

```java
-1
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

# Reward Points Example

```java
@PositiveOrZero
private Integer rewardPoints;
```

---

Valid:

```java
0
50
100
```

---

Invalid:

```java
-10
```

---

# Wallet Balance Example

```java
@PositiveOrZero
private BigDecimal walletBalance;
```

---

Valid:

```java
0
100.50
```

---

Invalid:

```java
-50.25
```

---

# Salary Example

```java
@PositiveOrZero
private Double bonus;
```

---

Valid:

```java
0.0
```

---

Valid:

```java
1000.0
```

---

Invalid:

```java
-1000.0
```

---

# Custom Message

Default:

```text
must be greater than or equal to 0
```

---

Custom:

```java
@PositiveOrZero(
    message = "Stock cannot be negative"
)
private Integer stock;
```

---

Error:

```json
{
    "message":
    "Stock cannot be negative"
}
```

---

# Using @PositiveOrZero with @Valid

DTO:

```java
public class ProductRequest {

    @PositiveOrZero
    private Integer stock;

}
```

---

Controller:

```java
@PostMapping("/products")
public String create(

        @Valid
        @RequestBody
        ProductRequest request) {

    return "saved";
}
```

---

Request:

```json
{
    "stock":-1
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
public class ProductController {

    @GetMapping

    public String search(

            @PositiveOrZero
            @RequestParam
            Integer page) {

        return "success";
    }

}
```

---

Valid:

```http
?page=0
```

---

Valid:

```http
?page=1
```

---

Invalid:

```http
?page=-1
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
public class InventoryService {

    public void updateStock(

            @PositiveOrZero
            Integer stock) {

    }

}
```

---

Call:

```java
updateStock(-5);
```

---

Result:

```java
ConstraintViolationException
```

---

# @PositiveOrZero vs @Positive

Most Frequently Asked Interview Question.

---

## @Positive

Checks:

```java
value > 0
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

## @PositiveOrZero

Checks:

```java
value >= 0
```

---

Valid:

```java
0
1
10
```

---

Invalid:

```java
-1
```

---

Comparison:

| Value | @Positive | @PositiveOrZero |
|---------|-----------|----------------|
| -1 | ❌ | ❌ |
| 0 | ❌ | ✅ |
| 1 | ✅ | ✅ |

---

# @PositiveOrZero vs @Min(0)

Another Interview Question.

---

## @Min(0)

```java
@Min(0)
private Integer stock;
```

---

Checks:

```java
value >= 0
```

---

## @PositiveOrZero

```java
@PositiveOrZero
private Integer stock;
```

---

Checks:

```java
value >= 0
```

---

For integer values:

```java
@Min(0)
```

and

```java
@PositiveOrZero
```

behave similarly.

---

# Key Difference

`@PositiveOrZero` works naturally with:

```java
Double
Float
BigDecimal
```

---

Example:

```java
@PositiveOrZero
private BigDecimal amount;
```

Valid:

```java
0.01
```

---

Using:

```java
@Min(0)
```

works but is generally less expressive for decimal values.

---

# Internal Implementation

Hibernate Validator provides:

```java
PositiveOrZeroValidatorForInteger
```

---

```java
PositiveOrZeroValidatorForLong
```

---

```java
PositiveOrZeroValidatorForDouble
```

---

```java
PositiveOrZeroValidatorForBigDecimal
```

---

depending on the field type.

---

# Internal Validation Logic

Example:

```java
@PositiveOrZero
private Integer stock;
```

Internally:

```java
return value >= 0;
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
PositiveOrZeroValidator
   │
   ▼
value >= 0 ?
   │
 ┌─Yes──────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting @PositiveOrZero to Reject Null

Wrong assumption:

```java
@PositiveOrZero
private Integer stock;
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
@PositiveOrZero
```

---

# Mistake 2

Using @Positive Instead

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

If zero should be allowed:

```java
@PositiveOrZero
```

---

# Mistake 3

Using on String

Wrong:

```java
@PositiveOrZero
private String quantity;
```

---

Not supported.

---

Use numeric types.

---

# Common Interview Questions

## What is @PositiveOrZero?

Validates that a numeric value is greater than or equal to zero.

---

## Does @PositiveOrZero allow zero?

Yes.

---

## Does @PositiveOrZero allow negative values?

No.

---

## Does @PositiveOrZero reject null?

No.

---

## Difference Between @Positive and @PositiveOrZero?

`@Positive` rejects zero.

`@PositiveOrZero` accepts zero.

---

## Difference Between @PositiveOrZero and @Min(0)?

For integer values they are similar.

`@PositiveOrZero` is more expressive and commonly preferred for business validations.

---

## Can @PositiveOrZero be used on BigDecimal?

Yes.

---

# Real-World Production Example

```java
public class ProductRequest {

    @NotNull

    @PositiveOrZero(
        message = "Stock cannot be negative"
    )
    private Integer stock;

    @NotNull

    @PositiveOrZero(
        message = "Price cannot be negative"
    )
    private BigDecimal price;

}
```

---

Request:

```json
{
    "stock":-5,
    "price":-100
}
```

---

Response:

```http
400 BAD REQUEST
```

```json
{
    "stock":"Stock cannot be negative",
    "price":"Price cannot be negative"
}
```

---

# Key Points To Remember

- `@PositiveOrZero` validates that a numeric value is greater than or equal to zero.
- Zero is allowed.
- Negative values are not allowed.
- Supports integer and decimal numeric types.
- Does NOT reject null values.
- Use `@NotNull` if null should fail validation.
- Commonly used for stock, balances, points, prices, and quantities.
- Similar to `@Min(0)` for integer values.
- Uses type-specific `PositiveOrZeroValidator` implementations internally.
- Frequently asked Spring Boot and Bean Validation interview annotation.