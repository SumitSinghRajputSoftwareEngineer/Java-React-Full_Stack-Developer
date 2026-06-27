
## Definition

`@CreditCardNumber` is a **Hibernate Validator-specific** annotation used to validate whether a credit card number is structurally valid.

It tells the validation framework:

> The value must be a valid credit card number according to the Luhn Algorithm.

Package:

```java
import org.hibernate.validator.constraints.CreditCardNumber;
```

---

# Important Interview Point

`@CreditCardNumber` is:

```text
NOT a Jakarta Bean Validation Standard Annotation
```

It is provided by:

```text
Hibernate Validator
```

---

# What Does It Actually Validate?

Many developers misunderstand this annotation.

`@CreditCardNumber` checks:

```text
Card Number Format
+
Luhn Checksum Algorithm
```

---

It does NOT check:

```text
Card Exists
Card Is Active
Card Has Balance
Card Has Expired
Card Belongs To User
```

---

Example:

This may pass validation:

```text
4111111111111111
```

even if no real card exists.

---

# Why Do We Need @CreditCardNumber?

Before processing payments, applications often need to verify:

```text
Card Number Format
```

Examples:

```text
E-Commerce
Payment Gateway
Subscription Service
Booking Systems
Banking Applications
```

---

# Basic Syntax

```java
@CreditCardNumber
private String cardNumber;
```

---

Meaning:

```text
cardNumber must pass Luhn validation
```

---

# Basic Example

```java
public class PaymentRequest {

    @CreditCardNumber
    private String cardNumber;

}
```

---

Valid:

```java
"4111111111111111"
```

---

Valid:

```java
"5555555555554444"
```

---

Invalid:

```java
"1234567890123456"
```

---

Invalid:

```java
"9999999999999999"
```

---

# Important Interview Question

## Does @CreditCardNumber reject null?

Answer:

```text
NO
```

---

Example:

```java
@CreditCardNumber
private String cardNumber;
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

Most validation annotations treat:

```java
null
```

as valid.

---

If mandatory:

```java
@NotBlank

@CreditCardNumber
private String cardNumber;
```

---

# Payment Request Example

```java
public class PaymentRequest {

    @CreditCardNumber(
        message =
        "Invalid credit card number"
    )
    private String cardNumber;

}
```

---

Valid:

```java
4111111111111111
```

---

Invalid:

```java
1234567890123456
```

---

# Custom Message

```java
@CreditCardNumber(
    message =
    "Please provide a valid credit card number"
)
private String cardNumber;
```

---

Response:

```json
{
   "cardNumber":
   "Please provide a valid credit card number"
}
```

---

# Using @CreditCardNumber with @Valid

DTO:

```java
public class PaymentRequest {

    @CreditCardNumber
    private String cardNumber;

}
```

---

Controller:

```java
@PostMapping("/payment")
public String pay(

        @Valid
        @RequestBody
        PaymentRequest request) {

    return "success";
}
```

---

Request:

```json
{
   "cardNumber":"1234567890123456"
}
```

---

Response:

```http
400 BAD REQUEST
```

---

# Combining with Other Validations

Production-grade example:

```java
@NotBlank

@CreditCardNumber
private String cardNumber;
```

---

Ensures:

```text
Card Number Exists
AND
Passes Luhn Validation
```

---

# Ignore Non-Digit Characters

One useful feature:

```java
@CreditCardNumber(
    ignoreNonDigitCharacters = true
)
```

---

Valid:

```java
4111-1111-1111-1111
```

---

Valid:

```java
4111 1111 1111 1111
```

---

Because:

```text
Spaces and Hyphens Ignored
```

---

Example:

```java
@CreditCardNumber(
    ignoreNonDigitCharacters = true
)
private String cardNumber;
```

---

# Strict Validation

```java
@CreditCardNumber(
    ignoreNonDigitCharacters = false
)
```

---

Valid:

```java
4111111111111111
```

---

Invalid:

```java
4111-1111-1111-1111
```

---

Invalid:

```java
4111 1111 1111 1111
```

---

# Luhn Algorithm Explained

Most Asked Interview Question.

---

Example Number:

```text
4111111111111111
```

---

Algorithm:

```text
Start from right
Double alternate digits
Subtract 9 if > 9
Sum all digits
Total must be divisible by 10
```

---

If:

```text
sum % 10 == 0
```

Validation passes.

---

# Example Luhn Check

Card:

```text
4111111111111111
```

---

Produces:

```text
Valid Luhn Checksum
```

---

Card:

```text
1234567890123456
```

---

Produces:

```text
Invalid Checksum
```

---

# What @CreditCardNumber Does NOT Validate

Interview Trap.

---

It DOES NOT verify:

```text
Real Card
Bank Existence
Expiry Date
CVV
Account Balance
Card Ownership
```

---

Only:

```text
Number Structure
+
Checksum
```

---

# @CreditCardNumber vs @Pattern

Interview Question.

---

## @CreditCardNumber

Checks:

```text
Luhn Algorithm
```

---

Example:

```java
@CreditCardNumber
```

---

## @Pattern

Checks:

```text
Regex Pattern
```

---

Example:

```java
@Pattern(
   regexp="\\d{16}"
)
```

---

Comparison:

| Feature | @CreditCardNumber | @Pattern |
|----------|------------------|----------|
| Length Check | Partial | Manual |
| Digits Check | Partial | Manual |
| Luhn Check | ✅ | ❌ |
| Card Validation | Better | Basic |

---

# @CreditCardNumber vs @Size

---

## @Size

Checks:

```text
String Length
```

---

Example:

```java
@Size(
    min = 16,
    max = 16
)
```

---

Does NOT validate:

```text
Checksum
```

---

## @CreditCardNumber

Checks:

```text
Luhn Algorithm
```

---

Much stronger validation.

---

# Internal Implementation

Hibernate Validator uses:

```java
CreditCardNumberValidator
```

---

Internally it executes:

```text
Luhn Algorithm
```

---

# Internal Validation Logic

Simplified:

```java
if(value == null)
    return true;

return passesLuhnCheck(value);
```

---

Equivalent:

```java
Luhn Validation
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
CreditCardNumberValidator
   │
   ▼
Luhn Check
   │
 ┌─Pass─────┐
 ▼          ▼
PASS      FAIL
```

---

# Common Mistakes

## Mistake 1

Expecting Real Card Verification

Wrong:

```text
Annotation checks if card exists.
```

---

Reality:

```text
Only Luhn checksum validation.
```

---

# Mistake 2

Expecting Null Rejection

Wrong:

```java
@CreditCardNumber
private String cardNumber;
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

@CreditCardNumber
```

---

# Mistake 3

Ignoring Expiry Validation

Wrong:

```java
@CreditCardNumber
```

alone.

---

Need additional validations for:

```text
Expiry Date
CVV
Card Holder Name
```

---

# Common Interview Questions

## What is @CreditCardNumber?

Validates a credit card number using the Luhn algorithm.

---

## Is it a standard Bean Validation annotation?

No.

Hibernate-specific.

---

## Does it verify a real credit card?

No.

---

## What algorithm does it use?

```text
Luhn Algorithm
```

---

## Does it reject null?

No.

---

## How do you make it mandatory?

```java
@NotBlank

@CreditCardNumber
```

---

## Can it ignore spaces and dashes?

Yes.

```java
ignoreNonDigitCharacters=true
```

---

# Real-World Production Example

```java
public class PaymentRequest {

    @NotBlank(
        message =
        "Card number is required"
    )

    @CreditCardNumber(
        ignoreNonDigitCharacters = true,
        message =
        "Invalid credit card number"
    )
    private String cardNumber;

}
```

---

Request:

```json
{
   "cardNumber":"4111-1111-1111-1111"
}
```

---

Result:

```text
PASS
```

because:

```java
ignoreNonDigitCharacters = true
```

---

# Key Points To Remember

- `@CreditCardNumber` validates card numbers using the Luhn algorithm.
- Hibernate Validator-specific annotation.
- Not part of Jakarta Bean Validation.
- Works on String fields.
- Does NOT verify card existence.
- Does NOT verify expiry date.
- Does NOT verify CVV.
- Does NOT reject null values.
- Use `@NotBlank` if mandatory.
- Supports ignoring spaces and hyphens.
- Internally uses `CreditCardNumberValidator`.
- Frequently asked Spring Boot and Hibernate Validator interview annotation.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@CreditCardNumber` | Credit Card Validation |
| `@Pattern` | Regex Validation |
| `@Size` | Length Validation |
| `@NotBlank` | Mandatory Text |
| `@Digits` | Numeric Digit Validation |

---

# Interview Shortcut

### Basic

```java
@CreditCardNumber
private String cardNumber;
```

### Production Version

```java
@NotBlank

@CreditCardNumber(
    ignoreNonDigitCharacters = true
)
private String cardNumber;
```

Meaning:

```text
Card Number Required
AND
Must Pass Luhn Validation
AND
Spaces/Hyphens Allowed
```

This is the most common enterprise Spring Boot usage of `@CreditCardNumber`.