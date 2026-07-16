# Security Best Practices for Logging

> **Goal:** Understand secure logging practices, avoiding sensitive data exposure, protecting user information, data masking techniques, compliance requirements, production security concerns, and interview concepts.

---

# 1. Introduction to Secure Logging

Logging is essential for:

```text
Monitoring

Debugging

Auditing

Troubleshooting
```

---

However, logging can become a serious security risk if sensitive information is stored in logs.

---

# Example

Bad Logging:

```java
log.info(
    "User Login: username={}, password={}",
    username,
    password
);
```

---

Generated Log:

```text
User Login:
username=john

password=admin123
```

---

Problem:

```text
Password Exposed
```

---

If someone gains access to logs:

```text
Credentials Can Be Stolen

Accounts Can Be Compromised

Compliance Violations Can Occur
```

---

# Why Logging Security Matters?

Logs are often:

```text
Stored Long-Term

Shared Across Teams

Sent To Monitoring Systems

Stored In Cloud Platforms
```

---

Sensitive data in logs can lead to:

```text
Data Breaches

Financial Loss

Legal Penalties

Compliance Failures
```

---

# Goal of Secure Logging

```text
Capture Useful Information

Without Exposing Sensitive Data
```

---

# Secure Logging Principle

```text
Log Events

Not Secrets
```

---

# 2. What is Sensitive Data?

## Definition

> Sensitive Data is information that can identify, authenticate, authorize, or expose a user, system, or organization.

---

# Examples

```text
Passwords

Credit Card Numbers

JWT Tokens

API Keys

Access Tokens

Bank Details

Personal Information

Secret Keys
```

---

# Why Protect Sensitive Data?

Because it can be used for:

```text
Identity Theft

Unauthorized Access

Financial Fraud

Account Takeover
```

---

# Common Sources

```text
HTTP Requests

Headers

Database Queries

API Responses

Authentication Systems
```

---

# Security Rule

```text
If Data Can Be Misused

Do Not Log It
```

---

# 3. Never Log Passwords

One of the most important security rules.

---

# Example

Bad Practice:

```java
log.info(
    "Login Request: {} {}",
    username,
    password
);
```

---

Generated Log:

```text
Login Request:

john

password123
```

---

# Why Dangerous?

Anyone with log access can:

```text
See User Credentials

Access Accounts

Perform Attacks
```

---

# Security Risk

```text
Critical
```

---

# Correct Approach

```java
log.info(
    "User Login Attempt: {}",
    username
);
```

---

Generated Log:

```text
User Login Attempt:
john
```

---

# Best Practice

```text
Log Username

Never Log Password
```

---

# Rule

```text
Passwords Must Never Appear
In Any Log File
```

---

# 4. Never Log Credit Card Information

Highly regulated information.

---

# Example

Bad Practice

```java
log.info(
    "Card Number: {}",
    cardNumber
);
```

---

Generated Log

```text
4111111111111111
```

---

# Risks

```text
Financial Fraud

Data Theft

PCI-DSS Violations
```

---

# PCI-DSS Requirement

Credit card data must be protected.

---

# Correct Approach

Mask the card number.

---

Example

```text
************1111
```

---

# Safe Logging

```java
log.info(
    "Payment Processed For Card {}",
    maskedCardNumber
);
```

---

# Result

```text
************1111
```

---

# Best Practice

```text
Show Only Last 4 Digits
```

---

# 5. Never Log JWT Tokens

Common mistake in REST APIs.

---

# Example JWT

```text
eyJhbGciOiJIUzI1Ni...
```

---

# Bad Logging

```java
log.info(
    "JWT Token: {}",
    jwtToken
);
```

---

# Problem

JWT may contain:

```text
User Identity

Roles

Permissions

Claims
```

---

If leaked:

```text
Account Access

Privilege Escalation

Impersonation
```

---

# Correct Approach

```java
log.info(
    "Authentication Token Received"
);
```

---

Or:

```java
log.info(
    "JWT Token Validated"
);
```

---

# Best Practice

```text
Log Token Events

Never Log Token Values
```

---

# 6. Never Log API Keys

API Keys are secrets.

---

# Example

```text
API_KEY=
abc123xyz789
```

---

# Bad Logging

```java
log.info(
    "API Key: {}",
    apiKey
);
```

---

# Risk

Anyone seeing logs can:

```text
Access External Services

Consume Resources

Perform Unauthorized Actions
```

---

# Correct Approach

```java
log.info(
    "External API Authentication Successful"
);
```

---

# Safe Output

```text
API Authenticated
```

---

# Rule

```text
Never Log Secret Keys
```

---

# Includes

```text
API Keys

Access Tokens

Refresh Tokens

Encryption Keys

Private Keys
```

---

# 7. Sensitive Data in HTTP Requests

Common source of accidental exposure.

---

# Example Request

```json
{
  "username":"john",
  "password":"secret123"
}
```

---

# Dangerous Logging

```java
log.info(
    "Request Body: {}",
    requestBody
);
```

---

# Result

```text
Entire Payload Logged
```

---

Including:

```text
Passwords

Personal Data

Secrets
```

---

# Safer Approach

Log only required fields.

---

Example

```java
log.info(
    "User Login Request Received"
);
```

---

# Benefits

```text
Useful Logging

No Sensitive Exposure
```

---

# 8. Sensitive Data in HTTP Headers

Headers frequently contain secrets.

---

# Example Headers

```http
Authorization:
Bearer eyJ...
```

---

```http
X-API-KEY:
abc123
```

---

# Bad Logging

```java
log.info(
    "Headers: {}",
    headers
);
```

---

# Risk

Secrets become visible in logs.

---

# Correct Approach

Mask sensitive headers.

---

Example

```text
Authorization=****
```

---

```text
X-API-KEY=****
```

---

# Best Practice

Never log:

```text
Authorization Header

API Keys

Access Tokens
```

---

# 9. Data Masking Techniques

One of the most important secure logging strategies.

---

# What is Data Masking?

## Definition

> Data Masking is the process of hiding sensitive information while preserving enough information for identification and troubleshooting.

---

# Goal

```text
Protect Sensitive Data

Retain Useful Context
```

---

# Example

Credit Card

---

Original

```text
4111111111111111
```

---

Masked

```text
************1111
```

---

# Example

Phone Number

---

Original

```text
9876543210
```

---

Masked

```text
******3210
```

---

# Example

Email

---

Original

```text
john.doe@gmail.com
```

---

Masked

```text
j***@gmail.com
```

---

# Benefits

```text
Security

Compliance

Troubleshooting
```

---

# 10. Implementing Data Masking

---

## Example

Mask Credit Card Number

```java
public String maskCard(
        String cardNumber) {

    return "************" +
            cardNumber.substring(
                    cardNumber.length() - 4
            );
}
```

---

# Output

```text
************1111
```

---

# Example

Mask Email

```java
public String maskEmail(
        String email) {

    return "***@" +
            email.split("@")[1];
}
```

---

# Output

```text
***@gmail.com
```

---

# Example

Mask API Key

```java
String maskedKey =
        "********";
```

---

# Output

```text
********
```

---

# Best Practice

Mask before logging.

---

Example

```java
log.info(
    "Card Number: {}",
    maskCard(cardNumber)
);
```

---

# 11. Compliance Considerations

Many regulations require secure logging.

---

# Common Standards

```text
GDPR

PCI-DSS

HIPAA

SOC 2

ISO 27001
```

---

# GDPR

Protects:

```text
Personal Data
```

---

# PCI-DSS

Protects:

```text
Credit Card Data
```

---

# HIPAA

Protects:

```text
Healthcare Information
```

---

# Logging Requirement

```text
Sensitive Data Must Be Protected
```

---

# Non-Compliance Risks

```text
Fines

Legal Issues

Reputation Damage
```

---

# 12. Secure Logging Architecture

Production Example.

---

```text
Application

      ↓

Mask Sensitive Data

      ↓

Generate Logs

      ↓

Logback

      ↓

Centralized Logging

      ↓

ELK / Splunk / Datadog
```

---

# Key Principle

```text
Mask Before Logging
```

---

Not:

```text
After Logging
```

---

# 13. Best Practices

---

## Never Log Passwords

Mandatory rule.

---

## Never Log JWT Tokens

Treat as secrets.

---

## Never Log API Keys

Protect credentials.

---

## Never Log Credit Card Numbers

Mask immediately.

---

## Mask Sensitive Fields

Examples:

```text
Emails

Phone Numbers

Card Numbers
```

---

## Review Logs Regularly

Identify accidental exposure.

---

## Secure Access To Logs

Only authorized personnel should access logs.

---

## Encrypt Log Storage

Protect logs at rest.

---

# Recommended Approach

```text
Collect Data

↓

Mask Sensitive Fields

↓

Generate Logs

↓

Store Securely
```

---

# 14. Common Mistakes

---

## Logging Entire Request Bodies

Problem:

```text
Sensitive Data Exposure
```

---

## Logging Authorization Headers

Problem:

```text
Token Leakage
```

---

## Logging JWT Tokens

Problem:

```text
Account Compromise
```

---

## Logging Database Credentials

Problem:

```text
Infrastructure Exposure
```

---

## Storing Unencrypted Logs

Problem:

```text
Data Breach Risk
```

---

# 15. Interview Questions

### Q1. Why should sensitive data not be logged?

Because logs may be accessed by unauthorized users and can expose confidential information.

---

### Q2. Name some sensitive data that should never be logged.

```text
Passwords

Credit Cards

JWT Tokens

API Keys

Access Tokens
```

---

### Q3. What is Data Masking?

Hiding sensitive information while preserving usability.

---

### Q4. Why should JWT tokens not be logged?

They may allow unauthorized access if exposed.

---

### Q5. How should credit card numbers be logged?

Using masking.

Example:

```text
************1111
```

---

### Q6. Why are API keys considered sensitive?

They provide access to protected systems and services.

---

### Q7. What is the biggest risk of logging passwords?

Account compromise.

---

### Q8. What does PCI-DSS protect?

Credit card information.

---

### Q9. What is the best place to mask sensitive data?

Before writing logs.

---

### Q10. What is the golden rule of secure logging?

```text
Log Events

Never Log Secrets
```

---

# Quick Revision

```text
Security Best Practices For Logging
-----------------------------------

Golden Rule

Log Events

Never Log Secrets

-----------------------------------

Never Log

✘ Passwords
✘ Credit Cards
✘ JWT Tokens
✘ API Keys
✘ Access Tokens
✘ Secret Keys

-----------------------------------

Sensitive Sources

✔ Request Bodies
✔ HTTP Headers
✔ API Responses
✔ Authentication Data

-----------------------------------

Data Masking

Purpose

Hide Sensitive Data

-----------------------------------

Examples

Card

4111111111111111

↓

************1111

-----------------------------------

Phone

9876543210

↓

******3210

-----------------------------------

Email

john@gmail.com

↓

j***@gmail.com

-----------------------------------

Benefits

✔ Security
✔ Compliance
✔ Safe Troubleshooting

-----------------------------------

Compliance

GDPR
PCI-DSS
HIPAA
SOC 2
ISO 27001

-----------------------------------

Best Practices

✔ Mask Before Logging
✔ Secure Log Storage
✔ Encrypt Logs
✔ Restrict Access
✔ Review Logs Regularly

-----------------------------------

Remember

Passwords

Never Log

JWT Tokens

Never Log

API Keys

Never Log

Credit Cards

Mask Before Logging

-----------------------------------

Security Principle

If It Is A Secret

Do Not Log It
```