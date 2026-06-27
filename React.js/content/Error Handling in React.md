# Error Handling in React

> **Goal:** Master React Error Handling completely — understand Error Boundaries, Try-Catch, API Error Handling, Fallback UI, Global Error Handling, React Error Lifecycle, Production Error Management, Real-World Examples, Best Practices, Common Mistakes, and Interview Questions.

---

# 1. What is Error Handling?

Error Handling means:

```text
Detecting Errors

Managing Errors

Recovering Gracefully

Preventing App Crashes
```

---

Without error handling:

```text
Single Error
      ↓

Entire App Crashes
```

---

Bad user experience.

---

Example:

```jsx
const user = null;

return (
  <h1>
    {user.name}
  </h1>
);
```

---

Error:

```text
Cannot Read Properties Of Null
```

---

Application crashes.

---

Need:

```text
Error Handling
```

---

# Why Error Handling is Important?

Large applications:

```text
Dashboard

Banking App

E-Commerce

Healthcare System
```

---

May encounter:

```text
Network Failures

Server Errors

Null Data

Unexpected Exceptions

Third-Party Failures
```

---

Without handling:

```text
White Screen Of Death
```

---

Users leave.

---

# Types of Errors in React

---

# 2. Runtime Errors

Occur while application runs.

---

Example:

```jsx
const user = null;

console.log(user.name);
```

---

Error:

```text
TypeError
```

---

# Syntax Errors

Code cannot compile.

---

Example:

```jsx
return (
 <div>
);
```

---

Missing tag.

---

Build fails.

---

# Network Errors

API requests fail.

---

Example:

```text
404 Not Found

500 Internal Server Error

Network Timeout
```

---

# Logical Errors

Application behaves incorrectly.

---

Example:

```jsx
price * quantity
```

---

Wrong formula.

---

Produces incorrect result.

---

# Error Boundaries

---

# 3. What are Error Boundaries?

React introduced:

```text
Error Boundaries
```

---

Purpose:

```text
Catch UI Rendering Errors
```

---

Definition:

> Error Boundaries are React components that catch JavaScript errors in child component trees and display fallback UI instead of crashing the entire application.

---

# Problem

Suppose:

```jsx
<App>
  <Header />
  <Dashboard />
  <Footer />
</App>
```

---

Dashboard crashes:

```text
Entire React App Crashes
```

---

Bad.

---

# Solution

Wrap with Error Boundary.

---

Flow:

```text
Dashboard Error
       ↓

Error Boundary Catches
       ↓

Fallback UI
       ↓

App Continues Running
```

---

# Error Boundary Visualization

```text
App
 ↓

ErrorBoundary
      ↓

 Dashboard
      ↓

 Error Occurs
      ↓

 Fallback UI
```

---

# Creating Error Boundary

Error Boundaries require:

```text
Class Components
```

---

Cannot currently be created directly using hooks.

---

# Example

```jsx
import React from "react";

class ErrorBoundary
extends React.Component {

 constructor(props) {

  super(props);

  this.state = {

   hasError:false

  };

 }

}
```

---

# getDerivedStateFromError()

Used to update state.

---

Example:

```jsx
static getDerivedStateFromError(
 error
){

 return {

  hasError:true

 };

}
```

---

Error occurs:

```text
hasError = true
```

---

# componentDidCatch()

Used for logging.

---

Example:

```jsx
componentDidCatch(
 error,
 errorInfo
){

 console.log(error);

 console.log(errorInfo);

}
```

---

Useful for:

```text
Monitoring

Analytics

Error Reporting
```

---

# Complete Error Boundary

```jsx
class ErrorBoundary
extends React.Component {

 constructor(props){

  super(props);

  this.state = {

   hasError:false

  };

 }

 static getDerivedStateFromError(){

  return {

   hasError:true

  };

 }

 componentDidCatch(
  error,
  errorInfo
 ){

  console.log(error);

 }

 render(){

  if(
   this.state.hasError
  ){

   return (

    <h1>

     Something Went Wrong

    </h1>

   );

  }

  return this.props.children;

 }

}
```

---

Usage:

```jsx
<ErrorBoundary>

 <Dashboard />

</ErrorBoundary>
```

---

# What Error Boundaries Catch?

---

# 4. Error Boundary Catches

✔ Rendering Errors

✔ Lifecycle Errors

✔ Constructor Errors

---

Example:

```jsx
render(){

 throw new Error();

}
```

---

Caught.

---

# What Error Boundaries Do NOT Catch?

❌ Event Handlers

❌ Async Code

❌ setTimeout

❌ API Calls

❌ Server Side Rendering

---

Example:

```jsx
<button
 onClick={() => {

  throw new Error();

 }}
>
```

---

Not caught.

---

Need:

```text
Try-Catch
```

---

# Try-Catch

---

# 5. What is Try-Catch?

JavaScript mechanism for:

```text
Handling Exceptions
```

---

Syntax:

```jsx
try {

}
catch(error){

}
```

---

# Example

```jsx
try {

 const result =
 JSON.parse(data);

}
catch(error){

 console.log(error);

}
```

---

If parsing fails:

```text
Catch Executes
```

---

Application continues.

---

# Real React Example

```jsx
function handleClick(){

 try{

  riskyFunction();

 }
 catch(error){

  console.log(error);

 }

}
```

---

Useful for:

```text
Event Handlers

Async Operations

Data Parsing
```

---

# Try-Catch Flow

```text
Try Block
      ↓

Error?

 NO
 ↓

Continue

 YES
 ↓

Catch Block
```

---

# API Error Handling

---

# 6. Why API Errors Occur?

Common reasons:

```text
Server Down

Bad Request

Unauthorized

Timeout

Network Failure
```

---

Must handle gracefully.

---

# Fetch API Example

```jsx
try{

 const response =

 await fetch("/users");

}
catch(error){

 console.log(error);

}
```

---

Handles:

```text
Network Errors
```

---

# Axios Example

```jsx
try{

 const response =

 await axios.get(
  "/users"
 );

}
catch(error){

 console.log(error);

}
```

---

Most common pattern.

---

# API Error State

Store error in state.

---

Example:

```jsx
const [

 error,

 setError

]

=
useState(null);
```

---

# Example

```jsx
try{

 const response =

 await axios.get(
  "/users"
 );

}
catch(error){

 setError(
  error.message
 );

}
```

---

Display:

```jsx
{
 error &&

 <p>

  {error}

 </p>

}
```

---

# Real World API State

---

# 7. Loading + Success + Error

Common pattern.

---

State:

```jsx
loading

data

error
```

---

Flow:

```text
Loading
    ↓

Success
 OR

Failure
```

---

Example:

```jsx
const [

 loading,

 setLoading

]

=
useState(true);

const [

 data,

 setData

]

=
useState([]);

const [

 error,

 setError

]

=
useState(null);
```

---

Request:

```jsx
try{

 setLoading(true);

 const response =

 await axios.get(
  "/users"
 );

 setData(
  response.data
 );

}
catch(error){

 setError(
  error.message
 );

}
finally{

 setLoading(false);

}
```

---

UI:

```jsx
if(loading){

 return <Spinner />;
}

if(error){

 return <ErrorPage />;
}
```

---

# Fallback UI

---

# 8. What is Fallback UI?

Fallback UI means:

```text
Alternative UI
Shown During Failure
```

---

Instead of:

```text
Blank Screen
```

---

Show:

```text
Error Message

Retry Button

Support Link
```

---

# Example

```jsx
<h1>

 Something Went Wrong

</h1>
```

---

Basic fallback.

---

# Better Example

```jsx
<div>

 <h2>

  Failed To Load

 </h2>

 <button>

  Retry

 </button>

</div>
```

---

Much better UX.

---

# Error Boundary Fallback

```jsx
if(hasError){

 return (

  <ErrorPage />

 );

}
```

---

# Global Error Handling

---

# 9. What is Global Error Handling?

Large applications need:

```text
Centralized Error Tracking
```

---

Instead of:

```text
Handling Errors
Everywhere
```

---

# Goal

```text
Capture All Errors

Log Errors

Monitor Production Issues
```

---

# Example Architecture

```text
App
 ↓

Global Error Handler
 ↓

API Layer
 ↓

Components
```

---

# Global Axios Error Handling

---

# 10. Axios Interceptors

Perfect for:

```text
Global API Errors
```

---

Example:

```jsx
axios.interceptors.response.use(

 response => response,

 error => {

  console.log(error);

  return Promise.reject(
   error
  );

 }

);
```

---

Every API error:

```text
Automatically Captured
```

---

# Global Authentication Handling

Example:

```text
401 Unauthorized
```

---

Interceptor:

```jsx
if(
 error.response.status
 === 401
){

 navigate("/login");

}
```

---

Automatically redirect.

---

# Global JavaScript Errors

---

# 11. Window Error Handler

Capture uncaught errors.

---

Example:

```jsx
window.onerror =

 function(

  message,

  source,

  lineno

 ){

  console.log(message);

 };
```

---

Captures:

```text
Unexpected Runtime Errors
```

---

# Error Monitoring Tools

---

# 12. Production Error Tracking

Large companies use:

- :contentReference[oaicite:0]{index=0}
- :contentReference[oaicite:1]{index=1}
- :contentReference[oaicite:2]{index=2}

---

Purpose:

```text
Track Production Errors

Stack Traces

Affected Users

Performance Data
```

---

# Example Flow

```text
Error Occurs
      ↓

Captured
      ↓

Sent To Monitoring Tool
      ↓

Developer Alert
```

---

# Error Handling Strategy

---

# 13. Enterprise Approach

UI Layer:

```text
Error Boundary
```

---

API Layer:

```text
Axios Interceptors
```

---

Business Layer:

```text
Try-Catch
```

---

Monitoring Layer:

```text
Sentry
```

---

Complete coverage.

---

# Common Mistakes

---

# 14. Empty Catch Blocks

Wrong:

```jsx
catch(error){

}
```

---

Error hidden.

---

Always:

```jsx
catch(error){

 console.log(error);

}
```

---

# Not Showing User-Friendly Messages

Wrong:

```text
TypeError:
Cannot Read Property...
```

---

Users don't understand.

---

Show:

```text
Something Went Wrong
Please Try Again
```

---

# Missing Loading States

Wrong:

```text
Error State Only
```

---

Need:

```text
Loading

Success

Error
```

---

All three.

---

# Throwing Errors Inside Render

Wrong:

```jsx
render(){

 throw new Error();

}
```

---

Unless intentionally testing.

---

# Best Practices

---

# 15. Always Handle API Failures

Never assume:

```text
API Always Works
```

---

# Use Error Boundaries

Protect:

```text
Pages

Widgets

Dashboards
```

---

# Log Errors

Useful for:

```text
Debugging

Monitoring

Analytics
```

---

# Show Friendly Messages

Avoid technical messages.

---

# Provide Retry Options

Example:

```jsx
<button>

 Retry

</button>
```

---

Improves UX.

---

# Error Handling Architecture

```text
User Action
      ↓

Component
      ↓

Try-Catch
      ↓

API Request
      ↓

Interceptor
      ↓

Error Logging
      ↓

Fallback UI
```

---

# Interview Questions

---

### Q1. What is an Error Boundary?

```text
React Component
That Catches UI Errors
```

---

### Q2. Which lifecycle methods are used?

```text
getDerivedStateFromError()

componentDidCatch()
```

---

### Q3. Can Error Boundaries catch API errors?

```text
NO
```

---

Need:

```text
Try-Catch
```

---

### Q4. Can Error Boundaries catch event handler errors?

```text
NO
```

---

### Q5. Why use Fallback UI?

```text
Prevent App Crash
Improve UX
```

---

### Q6. What is Global Error Handling?

```text
Centralized Error Management
```

---

### Q7. How do you globally handle API errors?

```text
Axios Interceptors
```

---

### Q8. What are common API states?

```text
Loading

Success

Error
```

---

### Q9. What is componentDidCatch used for?

```text
Error Logging
```

---

### Q10. Name some monitoring tools.

```text
Sentry

Datadog

New Relic
```

---

# Memory Tricks

### Error Boundary

```text
Catch UI Errors
```

---

### Try-Catch

```text
Catch JavaScript Errors
```

---

### Axios Interceptor

```text
Catch API Errors
```

---

### Fallback UI

```text
Show Alternative Screen
```

---

### Global Error Handler

```text
Central Error Control
```

---

# Complete Error Flow

```text
Error Occurs
      ↓

UI Error?
      ↓

Error Boundary
      ↓

Fallback UI

OR

API Error?
      ↓

Try-Catch
      ↓

Interceptor
      ↓

Error Message

OR

Unexpected Error
      ↓

Global Handler
      ↓

Monitoring Tool
```

---

# Quick Revision

```text
Error Handling
--------------

Error Boundary
 ↓
 Catch UI Errors

Try-Catch
 ↓
 Catch JS Errors

API Error Handling
 ↓
 Handle Network Failures

Fallback UI
 ↓
 Alternative Screen

Global Error Handling
 ↓
 Centralized Error Control

Tools:

Axios Interceptors
 ↓
 API Errors

Sentry
 ↓
 Production Monitoring

Remember:

UI Error
 ↓
 Error Boundary

API Error
 ↓
 Try-Catch

Global Tracking
 ↓
 Sentry
```

---