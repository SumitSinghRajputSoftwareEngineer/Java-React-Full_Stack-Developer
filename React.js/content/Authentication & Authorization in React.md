# Authentication & Authorization in React

> **Goal:** Master Authentication and Authorization in React completely — understand login systems, JWT Authentication, Login Flow, Protected Routes, Role-Based Access Control (RBAC), Refresh Tokens, Session Management, security best practices, frontend vs backend responsibilities, real-world architecture, interview questions, and production-ready patterns.

---

# 1. Authentication vs Authorization

One of the most asked interview questions.

---

Many developers confuse:

```text
Authentication
```

and

```text
Authorization
```

---

They are completely different.

---

# Authentication

Authentication answers:

```text
Who Are You?
```

---

Example:

User enters:

```text
Username

Password
```

---

System verifies identity.

---

Result:

```text
User Authenticated
```

---

Examples:

```text
Login

OTP Verification

Google Login

GitHub Login
```

---

# Authorization

Authorization answers:

```text
What Can You Access?
```

---

Example:

Authenticated user:

```text
Employee
```

---

Can access:

```text
Employee Dashboard
```

---

Cannot access:

```text
Admin Panel
```

---

This is Authorization.

---

# Simple Analogy

Airport Example:

Authentication:

```text
Show Passport
```

---

Authorization:

```text
Which Areas Can You Enter?
```

---

VIP Lounge:

```text
Authorized
```

---

Normal Passenger:

```text
Not Authorized
```

---

# Visualization

```text
Login
  ↓

Authentication
  ↓

Identity Verified
  ↓

Authorization
  ↓

Permissions Checked
```

---

# Real World Example

---

User:

```text
john@example.com
```

---

Logs in.

---

Authentication:

```text
Valid User?
```

---

YES.

---

Authorization:

```text
Role = ADMIN
```

---

Access:

```text
Admin Dashboard
```

---

Allowed.

---

# JWT Authentication

---

# 2. What is JWT?

JWT stands for:

```text
JSON Web Token
```

---

Very common in:

```text
React

Spring Boot

Node.js

Microservices
```

---

JWT is used to:

```text
Authenticate Users
```

without storing session data on server.

---

# JWT Structure

A JWT consists of:

```text
Header
Payload
Signature
```

---

Example:

```text
xxxxx.yyyyy.zzzzz
```

---

Visualization:

```text
Header
   .
Payload
   .
Signature
```

---

# Header

Contains:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

---

Defines:

```text
Algorithm
Token Type
```

---

# Payload

Contains user data.

---

Example:

```json
{
  "userId": 101,
  "username": "john",
  "role": "ADMIN"
}
```

---

Known as:

```text
Claims
```

---

# Signature

Used to verify token integrity.

---

Generated using:

```text
Secret Key
```

---

Prevents tampering.

---

# JWT Flow

```text
Login
  ↓

Backend Verifies Credentials
  ↓

Backend Creates JWT
  ↓

JWT Sent To Client
  ↓

Client Stores Token
  ↓

Token Sent With Requests
```

---

# Login Flow

---

# 3. Complete Login Flow

React Application:

```text
Login Page
```

---

User enters:

```text
Email
Password
```

---

Example:

```jsx
const credentials = {

 email:
 "john@gmail.com",

 password:
 "password123"

};
```

---

Send request:

```http
POST /login
```

---

Backend verifies.

---

If valid:

```json
{
 "token":"jwt_token_here"
}
```

---

Frontend stores token.

---

Then:

```text
User Logged In
```

---

# Complete Flow Diagram

```text
User
 ↓

Login Form
 ↓

React App
 ↓

POST /login
 ↓

Spring Boot
 ↓

Validate Credentials
 ↓

Generate JWT
 ↓

Return Token
 ↓

Store Token
 ↓

Authenticated
```

---

# React Login Example

```jsx
const handleLogin =
async () => {

 const response =
 await axios.post(
  "/login",
  credentials
 );

 localStorage.setItem(
  "token",
  response.data.token
 );

};
```

---

Token stored.

---

User authenticated.

---

# Where Should JWT Be Stored?

Most important interview topic.

---

Options:

```text
Local Storage

Session Storage

Cookies
```

---

# Local Storage

```javascript
localStorage.setItem(
 "token",
 jwt
);
```

---

Advantages:

```text
Easy
Persistent
```

---

Disadvantages:

```text
XSS Risk
```

---

# Session Storage

```javascript
sessionStorage.setItem(
 "token",
 jwt
);
```

---

Cleared when tab closes.

---

More secure than local storage.

---

# HttpOnly Cookie

Best production approach.

---

Advantages:

```text
Cannot Be Accessed By JavaScript
```

---

Protects against:

```text
XSS Attacks
```

---

Most enterprise applications use:

```text
JWT + HttpOnly Cookie
```

---

# Sending JWT in Requests

---

# 4. Authorization Header

Every API request includes:

```http
Authorization:
Bearer TOKEN
```

---

Example:

```http
Authorization:
Bearer eyJhb...
```

---

Axios Example:

```jsx
axios.get("/users", {

 headers: {

  Authorization:
  `Bearer ${token}`

 }

});
```

---

Backend validates JWT.

---

If valid:

```text
Access Granted
```

---

# Protected Routes

---

# 5. What Are Protected Routes?

Protected Routes prevent unauthorized access.

---

Example:

```text
Dashboard
```

should only be visible after login.

---

Without protection:

```text
Anyone Can Access
```

---

Bad.

---

Need:

```text
Protected Route
```

---

# Route Structure

```text
Login
Dashboard
Profile
Admin
```

---

Protected:

```text
Dashboard
Profile
Admin
```

---

# Example

```jsx
<Route

 path="/dashboard"

 element={

  <ProtectedRoute>

   <Dashboard />

  </ProtectedRoute>

 }

/>
```

---

# ProtectedRoute Component

```jsx
import { Navigate }
from "react-router-dom";

function ProtectedRoute({
 children
}) {

 const token =
 localStorage.getItem(
  "token"
 );

 return token
  ? children
  : <Navigate
      to="/login"
    />;

}
```

---

Flow:

```text
Has Token?
   ↓

YES
 ↓

Dashboard

NO
 ↓

Redirect Login
```

---

# Visualization

```text
User Opens Dashboard
         ↓

Check Token
         ↓

Exists?
    ↓        ↓

 YES       NO
  ↓         ↓

Dashboard  Login
```

---

# Role-Based Access Control (RBAC)

---

# 6. What is Role-Based Access?

Not all users are equal.

---

Example:

Roles:

```text
ADMIN

MANAGER

EMPLOYEE
```

---

Permissions differ.

---

# Example

Employee:

```text
View Profile
```

---

Admin:

```text
Create Users

Delete Users

Manage Roles
```

---

# JWT Payload Example

```json
{
 "userId":1,
 "role":"ADMIN"
}
```

---

Role available inside token.

---

# Role Check

```jsx
const role =
decodedToken.role;
```

---

# Admin Route

```jsx
function AdminRoute({
 children
}) {

 const role =
 getUserRole();

 return role === "ADMIN"

 ? children

 : <Navigate
     to="/unauthorized"
   />;

}
```

---

Usage:

```jsx
<Route

 path="/admin"

 element={

  <AdminRoute>

   <AdminPage />

  </AdminRoute>

 }

/>
```

---

# RBAC Flow

```text
User Login
      ↓

JWT Contains Role
      ↓

ADMIN ?
      ↓

YES
 ↓

Admin Dashboard

NO
 ↓

Unauthorized
```

---

# Refresh Tokens

---

# 7. Problem With JWT Expiration

JWT should expire.

---

Example:

```text
15 Minutes
```

---

Why?

Security.

---

If token stolen:

```text
Cannot Be Used Forever
```

---

# Problem

After 15 minutes:

```text
User Logged Out
```

---

Poor experience.

---

Need:

```text
Refresh Token
```

---

# Access Token vs Refresh Token

Access Token:

```text
Short Lived
```

Example:

```text
15 Minutes
```

---

Refresh Token:

```text
Long Lived
```

Example:

```text
7 Days
```

---

# Flow

```text
Login
 ↓

Access Token
 ↓

Refresh Token
```

---

Access token expires.

---

Frontend sends:

```text
Refresh Token
```

---

Backend issues:

```text
New Access Token
```

---

User stays logged in.

---

# Refresh Token Flow

```text
User Logged In
      ↓

Access Token Expired
      ↓

401 Unauthorized
      ↓

Send Refresh Token
      ↓

Backend Generates New Token
      ↓

Retry Request
```

---

# Axios Refresh Example

```jsx
axios.interceptors.response.use(

 response => response,

 async error => {

  if(
   error.response.status
   === 401
  ) {

   await refreshToken();

  }

 }

);
```

---

Very common enterprise pattern.

---

# Session Management

---

# 8. What is Session Management?

Tracks:

```text
Logged-In Users
```

---

Two approaches:

```text
Traditional Sessions

JWT Sessions
```

---

# Traditional Session

Server stores:

```text
Session Data
```

---

Example:

```text
UserId

Permissions

Preferences
```

---

Server Memory:

```text
Session -> User
```

---

Frontend stores:

```text
Session ID
```

---

# JWT Session

Server stores:

```text
Nothing
```

---

Token contains:

```text
User Information
```

---

Known as:

```text
Stateless Authentication
```

---

# Traditional Session Flow

```text
Login
 ↓

Server Creates Session
 ↓

Session ID Returned
 ↓

Browser Stores Cookie
 ↓

Every Request Sends Session ID
```

---

# JWT Session Flow

```text
Login
 ↓

JWT Generated
 ↓

Browser Stores Token
 ↓

Every Request Sends JWT
```

---

No session lookup required.

---

# Logout Flow

---

# 9. Logout Implementation

Simple approach:

```jsx
localStorage.removeItem(
 "token"
);
```

---

Redirect:

```jsx
navigate("/login");
```

---

User logged out.

---

# Production Logout

Also invalidate:

```text
Refresh Token
```

---

Prevents token reuse.

---

# Authentication Architecture

---

# 10. Enterprise Flow

```text
React App
      ↓

Login Request
      ↓

Spring Security
      ↓

JWT Generated
      ↓

Access Token
      ↓

Refresh Token
      ↓

Protected APIs
      ↓

Authorization Checks
      ↓

Role Validation
      ↓

Response
```

---

# Common Mistakes

---

# 11. Storing Passwords in Frontend

Wrong:

```javascript
localStorage.setItem(
 "password",
 password
);
```

---

Never do this.

---

# Trusting Frontend Roles

Wrong:

```jsx
if(role === "ADMIN")
```

---

Frontend checks are useful.

---

But backend MUST verify too.

---

Never trust frontend alone.

---

# Not Expiring Tokens

Wrong:

```text
Token Never Expires
```

---

Huge security risk.

---

# Storing JWT In Plain Local Storage

Possible.

---

But:

```text
HttpOnly Cookies
```

are usually safer.

---

# Best Practices

---

# 12. Production Recommendations

✔ Use JWT Authentication

✔ Use Access + Refresh Tokens

✔ Protect routes

✔ Implement RBAC

✔ Use HTTPS

✔ Use token expiration

✔ Validate roles on backend

✔ Use HttpOnly cookies when possible

✔ Handle token refresh automatically

✔ Secure logout flow

---

# Interview Questions

---

### Q1. Authentication vs Authorization?

Authentication:

```text
Who Are You?
```

---

Authorization:

```text
What Can You Access?
```

---

### Q2. What is JWT?

```text
JSON Web Token
```

---

### Q3. JWT Structure?

```text
Header

Payload

Signature
```

---

### Q4. What is a Protected Route?

```text
Route Accessible
Only After Login
```

---

### Q5. What is RBAC?

```text
Role-Based Access Control
```

---

### Q6. Access Token vs Refresh Token?

Access Token:

```text
Short Lived
```

---

Refresh Token:

```text
Long Lived
```

---

### Q7. Where should JWT be stored?

Best:

```text
HttpOnly Cookie
```

---

Common:

```text
Local Storage
```

---

### Q8. Why use Refresh Tokens?

```text
Generate New Access Token
Without Re-login
```

---

### Q9. Does frontend authorization provide security?

```text
NO
```

---

Backend must verify.

---

### Q10. What is Stateless Authentication?

```text
Server Stores No Session
Data
```

---

JWT-based systems.

---

# Memory Tricks

### Authentication

```text
Who Are You?
```

---

### Authorization

```text
What Can You Access?
```

---

### JWT

```text
Identity Token
```

---

### Protected Route

```text
Token Required
```

---

### RBAC

```text
Role Based Access
```

---

### Access Token

```text
Short Lived
```

---

### Refresh Token

```text
Long Lived
```

---

### Session

```text
Track Logged-In Users
```

---

# Complete Authentication Flow

```text
User Login
      ↓

Credentials Sent
      ↓

Backend Validation
      ↓

JWT Generated
      ↓

Store Token
      ↓

Protected Route Access
      ↓

Authorization Check
      ↓

Role Validation
      ↓

API Access Granted
      ↓

Access Token Expires
      ↓

Refresh Token Used
      ↓

New Access Token
      ↓

Continue Working
```

---

# Quick Revision

```text
Authentication & Authorization
------------------------------

Authentication
 ↓
 Who Are You?

Authorization
 ↓
 What Can You Access?

JWT
 ↓
 Header
 Payload
 Signature

Protected Routes
 ↓
 Require Login

RBAC
 ↓
 Role-Based Access

Access Token
 ↓
 Short Lived

Refresh Token
 ↓
 Long Lived

Session Types
 ↓
 Traditional Session

 JWT Session

Best Practices:

✔ JWT

✔ Refresh Tokens

✔ Protected Routes

✔ RBAC

✔ Token Expiry

✔ Backend Validation

Remember:

Authentication
 ↓
 Identity

Authorization
 ↓
 Permission
```

---
