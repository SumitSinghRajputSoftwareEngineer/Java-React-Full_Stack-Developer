# API Integration in React

> **Goal:** Master API Integration in React — understand how React communicates with backend servers, Fetch API, Axios, GET/POST/PUT/PATCH/DELETE requests, Error Handling, Loading States, Retry Logic, Interceptors, Request Lifecycle, Real-World Examples, Best Practices, and Interview Questions.

---

# 1. What is API Integration?

Modern React applications rarely work alone.

---

React UI needs data from:

```text
Backend Server

Database

Microservices

Third-Party Services
```

---

Examples:

```text
Weather App

E-Commerce App

Banking App

Social Media App
```

---

Need data from server.

---

This communication is called:

```text
API Integration
```

---

# Example

Frontend:

```text
React
```

---

Backend:

```text
Spring Boot
```

---

Flow:

```text
React
   ↓ HTTP Request

Spring Boot
   ↓

Database
   ↓

Response

React UI Updates
```

---

# Real World Example

Login Screen:

```text
Username

Password
```

---

Click Login:

```text
React Sends Request
```

---

Backend verifies.

---

Response:

```json
{
  "token": "abc123"
}
```

---

React stores token.

---

User logged in.

---

# What is an API?

API stands for:

```text
Application Programming Interface
```

---

API allows:

```text
Applications To Communicate
```

---

Example:

```text
React
    ↔
Spring Boot
```

---

Most modern APIs use:

```text
REST API
```

---

# REST API

Uses HTTP methods:

```text
GET

POST

PUT

PATCH

DELETE
```

---

Each has a different purpose.

---

# API Request Lifecycle

---

# 2. Complete Flow

```text
React Component
       ↓

Send Request
       ↓

Server Receives
       ↓

Business Logic
       ↓

Database
       ↓

Response
       ↓

React Updates UI
```

---

Example:

```text
Load Users
```

---

```jsx
useEffect(() => {

 fetchUsers();

}, []);
```

---

Component mounts.

---

API called.

---

Data returned.

---

UI updates.

---

# Fetch API

---

# 3. What is Fetch API?

Built-in browser API.

---

No installation needed.

---

Available in:

```text
Chrome

Firefox

Edge

Safari
```

---

Syntax:

```jsx
fetch(url);
```

---

Returns:

```text
Promise
```

---

# Basic Example

```jsx
fetch(
 "https://api.example.com/users"
);
```

---

Response object returned.

---

Need conversion:

```jsx
response.json();
```

---

# Complete Example

```jsx
useEffect(() => {

 fetch(
  "https://jsonplaceholder.typicode.com/users"
 )

 .then(response =>
   response.json()
 )

 .then(data =>
   console.log(data)
 );

}, []);
```

---

Output:

```text
Array Of Users
```

---

# Async Await Version

Preferred approach.

---

```jsx
async function getUsers() {

 const response =

 await fetch(
  "https://jsonplaceholder.typicode.com/users"
 );

 const data =

 await response.json();

 console.log(data);

}
```

---

Cleaner.

---

# Internal Working

```text
Request Sent
      ↓

Promise Created
      ↓

Response Received
      ↓

Convert JSON
      ↓

Update State
```

---

# Axios

---

# 4. What is Axios?

Popular HTTP client library.

---

Install:

```bash
npm install axios
```

---

Import:

```jsx
import axios
from "axios";
```

---

Benefits:

```text
Simpler Syntax

Automatic JSON Parsing

Interceptors

Timeouts

Better Error Handling
```

---

# Basic Example

```jsx
const response =

await axios.get(
 "https://api.com/users"
);
```

---

Data:

```jsx
response.data
```

---

Unlike Fetch:

```jsx
response.json()
```

Not needed.

---

# Fetch vs Axios

| Feature | Fetch | Axios |
|----------|----------|----------|
| Built Into Browser | Yes | No |
| Installation | No | Yes |
| JSON Parsing | Manual | Automatic |
| Interceptors | No | Yes |
| Timeout Support | Limited | Easy |
| Error Handling | Basic | Better |

---

# GET Request

---

# 5. What is GET?

Used to:

```text
Read Data
```

---

Example:

```text
Get Users

Get Products

Get Orders
```

---

HTTP:

```http
GET /users
```

---

# Fetch GET

```jsx
const response =

await fetch("/users");

const users =

await response.json();
```

---

# Axios GET

```jsx
const response =

await axios.get(
 "/users"
);

console.log(
 response.data
);
```

---

# Response Example

```json
[
 {
  "id":1,
  "name":"John"
 }
]
```

---

# POST Request

---

# 6. What is POST?

Used to:

```text
Create New Data
```

---

Example:

```text
Create User

Place Order

Register Account
```

---

HTTP:

```http
POST /users
```

---

# Fetch POST

```jsx
await fetch(

 "/users",

 {

  method:"POST",

  headers:{

   "Content-Type":
   "application/json"

  },

  body:JSON.stringify({

   name:"John"

  })

 }

);
```

---

# Axios POST

```jsx
await axios.post(

 "/users",

 {

  name:"John"

 }

);
```

---

Much cleaner.

---

# Request Body

```json
{
 "name":"John"
}
```

---

# PUT Request

---

# 7. What is PUT?

Used to:

```text
Replace Entire Resource
```

---

Example:

Existing User:

```json
{
 "id":1,
 "name":"John",
 "age":20
}
```

---

PUT:

```json
{
 "id":1,
 "name":"David",
 "age":30
}
```

---

Entire object replaced.

---

# Axios PUT

```jsx
await axios.put(

 "/users/1",

 {

  name:"David",

  age:30

 }

);
```

---

# Fetch PUT

```jsx
await fetch(

 "/users/1",

 {

  method:"PUT",

  body:JSON.stringify({

   name:"David",

   age:30

  })

 }

);
```

---

# PATCH Request

---

# 8. What is PATCH?

Used to:

```text
Partial Update
```

---

Example:

Current User:

```json
{
 "id":1,
 "name":"John",
 "age":20
}
```

---

Update only:

```json
{
 "age":21
}
```

---

Everything else remains unchanged.

---

# Axios PATCH

```jsx
await axios.patch(

 "/users/1",

 {

  age:21

 }

);
```

---

# DELETE Request

---

# 9. What is DELETE?

Used to:

```text
Remove Resource
```

---

Example:

```http
DELETE /users/1
```

---

Deletes:

```text
User 1
```

---

# Axios DELETE

```jsx
await axios.delete(
 "/users/1"
);
```

---

# Fetch DELETE

```jsx
await fetch(

 "/users/1",

 {

  method:"DELETE"

 }

);
```

---

# Error Handling

---

# 10. Why Error Handling?

APIs can fail.

---

Reasons:

```text
Network Failure

Server Down

Timeout

Unauthorized

Bad Request
```

---

Need handling.

---

# Try Catch

```jsx
try {

 const response =

 await axios.get(
  "/users"
 );

}
catch(error) {

 console.log(error);

}
```

---

# Fetch Error Handling

```jsx
try {

 const response =

 await fetch("/users");

 if(!response.ok){

  throw new Error(
   "Failed"
  );

 }

}
catch(error){

 console.log(error);

}
```

---

Important:

```text
Fetch Does NOT Throw
For HTTP Errors
```

---

Need manual check.

---

# Loading States

---

# 11. Why Loading State?

API takes time.

---

Without loading:

```text
Blank Screen
```

---

Bad user experience.

---

# Example

```jsx
const [loading,
setLoading]

=
useState(false);
```

---

Before API:

```jsx
setLoading(true);
```

---

After API:

```jsx
setLoading(false);
```

---

Render:

```jsx
if(loading){

 return <h1>
  Loading...
 </h1>;
}
```

---

Flow:

```text
Request Started
      ↓

Loading=true
      ↓

Spinner
      ↓

Response Received
      ↓

Loading=false
      ↓

Show Data
```

---

# Real Example

```jsx
const [users,
setUsers]

=
useState([]);
```

---

```jsx
const [loading,
setLoading]

=
useState(true);
```

---

```jsx
useEffect(() => {

 async function loadUsers() {

  try {

   const response =

   await axios.get(
    "/users"
   );

   setUsers(
    response.data
   );

  }
  finally {

   setLoading(false);

  }

 }

 loadUsers();

}, []);
```

---

# Retry Logic

---

# 12. Why Retry Logic?

Temporary failures happen.

---

Example:

```text
Network Timeout

Server Busy

Internet Fluctuation
```

---

Instead of:

```text
Immediate Failure
```

---

Retry request.

---

# Example

```jsx
async function fetchData() {

 let retries = 3;

 while(retries > 0){

  try {

   const response =

   await axios.get(
    "/users"
   );

   return response.data;

  }
  catch(error){

   retries--;

  }

 }

}
```

---

Flow:

```text
Attempt 1
   ↓

Fail

Attempt 2
   ↓

Fail

Attempt 3
   ↓

Success
```

---

# Exponential Backoff

Professional systems use:

```text
Increasing Delay
```

---

Example:

```text
1 sec

2 sec

4 sec

8 sec
```

---

Reduces server load.

---

# API Interceptors

---

# 13. What are Interceptors?

Middleware for requests and responses.

---

Can modify:

```text
Request

Response
```

---

Before reaching application.

---

Only available in:

```text
Axios
```

---

# Visualization

```text
Request
   ↓

Interceptor
   ↓

Server
   ↓

Response
   ↓

Interceptor
   ↓

React
```

---

# Request Interceptor

---

# 14. Use Case

Automatically attach:

```text
JWT Token
```

---

Instead of:

```jsx
headers:{
 Authorization:"Bearer token"
}
```

Everywhere.

---

Use interceptor.

---

# Example

```jsx
axios.interceptors.request.use(

 config => {

  config.headers.Authorization =

  `Bearer ${token}`;

  return config;

 }

);
```

---

Now every request gets token.

---

# Response Interceptor

---

# 15. Use Case

Handle:

```text
401 Unauthorized
```

Globally.

---

Example:

```jsx
axios.interceptors.response.use(

 response => response,

 error => {

  if(

   error.response.status
   === 401

  ){

   navigate("/login");

  }

  return Promise.reject(
   error
  );

 }

);
```

---

Flow:

```text
Expired Token
      ↓

401
      ↓

Interceptor
      ↓

Redirect Login
```

---

# Creating API Instance

---

# 16. Best Practice

Create:

```jsx
api.js
```

---

Example:

```jsx
import axios
from "axios";
```

---

```jsx
const api =

axios.create({

 baseURL:

 "https://api.company.com"

});
```

---

Use:

```jsx
api.get("/users");
```

---

Cleaner.

---

# Complete API Flow

---

# 17. Real Application Flow

```text
Component Mount
      ↓

Loading=true
      ↓

API Request
      ↓

Interceptor
      ↓

Backend
      ↓

Response
      ↓

Interceptor
      ↓

Update State
      ↓

Loading=false
      ↓

Render UI
```

---

# Common Mistakes

---

# 18. Missing Error Handling

Wrong:

```jsx
await axios.get("/users");
```

---

Without:

```jsx
try/catch
```

---

Application may crash.

---

# 19. No Loading State

Wrong:

```text
Blank Screen
```

---

Always show:

```text
Loading Indicator
```

---

# 20. API Calls Inside Render

Wrong:

```jsx
function App() {

 axios.get("/users");

}
```

---

Causes:

```text
Infinite Requests
```

---

Use:

```jsx
useEffect()
```

---

# 21. Repeating Base URL

Wrong:

```jsx
axios.get(
 "https://api.com/users"
);

axios.get(
 "https://api.com/orders"
);
```

---

Use:

```jsx
axios.create()
```

---

# Best Practices

---

# 22. Use Axios for Large Projects

Provides:

```text
Interceptors

Timeouts

Cleaner API
```

---

# 23. Always Handle Errors

Use:

```jsx
try/catch
```

---

# 24. Always Show Loading State

Improves UX.

---

# 25. Use Central API Layer

Example:

```text
api.js

userService.js

productService.js
```

---

Cleaner architecture.

---

# 26. Use Interceptors for Authentication

Avoid repeating:

```text
JWT Token Logic
```

---

# Interview Questions

---

### Q1. What is API Integration?

```text
Communication Between
Frontend And Backend
```

---

### Q2. Fetch vs Axios?

Fetch:

```text
Built-In Browser API
```

---

Axios:

```text
Feature Rich HTTP Client
```

---

### Q3. Which HTTP Method Retrieves Data?

```text
GET
```

---

### Q4. Which Method Creates Data?

```text
POST
```

---

### Q5. PUT vs PATCH?

PUT:

```text
Replace Entire Resource
```

---

PATCH:

```text
Partial Update
```

---

### Q6. Which Method Deletes Data?

```text
DELETE
```

---

### Q7. Why Use Loading State?

```text
Improve User Experience
```

---

### Q8. What are Interceptors?

```text
Request/Response Middleware
```

---

### Q9. Why Use Retry Logic?

```text
Handle Temporary Failures
```

---

### Q10. Why Use Axios Instance?

```text
Reusable Configuration
```

---

# Memory Tricks

### GET

```text
Read Data
```

---

### POST

```text
Create Data
```

---

### PUT

```text
Replace Data
```

---

### PATCH

```text
Update Partial Data
```

---

### DELETE

```text
Remove Data
```

---

### Loading State

```text
Request In Progress
```

---

### Error Handling

```text
Handle Failures
```

---

### Retry Logic

```text
Try Again
```

---

### Interceptors

```text
Request/Response Middleware
```

---

# Complete CRUD Mapping

```text
Create
 ↓
POST

Read
 ↓
GET

Update Entire
 ↓
PUT

Update Partial
 ↓
PATCH

Delete
 ↓
DELETE
```

---

# Quick Revision

```text
API Integration
---------------

Purpose:
Frontend ↔ Backend Communication

Tools:

Fetch API

Axios

HTTP Methods:

GET
 ↓ Read

POST
 ↓ Create

PUT
 ↓ Replace

PATCH
 ↓ Partial Update

DELETE
 ↓ Remove

Additional Concepts:

Loading State

Error Handling

Retry Logic

Interceptors

Best Practice:

✔ Axios Instance

✔ Error Handling

✔ Loading State

✔ Retry Logic

✔ Interceptors

Remember:

Request
   ↓

Server
   ↓

Response
   ↓

Update UI
```

---