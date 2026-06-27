# React Router

> **Goal:** Master React Router completely — understand Routing, SPA Navigation, BrowserRouter, Routes, Route, Link, NavLink, useNavigate, useParams, useLocation, Nested Routing, Protected Routes, Dynamic Routes, Route Parameters, Lazy Loaded Routes, Internal Working, Route Matching, Real-World Examples, Performance Optimization, and Interview Questions.

---

# 1. What is Routing?

Routing means:

```text
Showing Different Pages
Based On URL
```

---

Example:

```text
/home

/products

/cart

/profile
```

---

Each URL shows:

```text
Different Component
```

---

Traditional websites:

```text
New Page Request
To Server
```

---

React applications:

```text
No Full Page Reload
```

---

React changes:

```text
Only Required Components
```

---

This is called:

```text
Client Side Routing
```

---

# Real World Example

E-commerce website:

```text
/
/products
/products/101
/cart
/orders
/profile
```

---

Every route displays:

```text
Different UI
```

---

# Why Routing Is Needed?

Without routing:

```text
One Huge Component
```

---

Application becomes:

```text
Hard To Maintain

Hard To Scale
```

---

Routing allows:

```text
Page Separation
```

---

# Routing Types

---

## Server Side Routing

Traditional websites.

---

Flow:

```text
User Clicks Link
       ↓

Request Sent To Server
       ↓

Server Returns New HTML
       ↓

Browser Reloads Page
```

---

Examples:

```text
PHP

ASP.NET

JSP
```

---

## Client Side Routing

React.

---

Flow:

```text
User Clicks Link
       ↓

URL Changes
       ↓

React Changes Component
       ↓

No Page Reload
```

---

Faster.

---

# React Router

---

# 2. What is React Router?

React Router is the official routing library for React.

---

Install:

```bash
npm install react-router-dom
```

---

Package:

```text
react-router-dom
```

---

Provides:

```text
BrowserRouter

Routes

Route

Link

NavLink

Hooks
```

---

# BrowserRouter

---

# 3. What is BrowserRouter?

BrowserRouter enables routing.

---

Without it:

```text
Routing Does Not Work
```

---

Example:

```jsx
import {

 BrowserRouter

}
from "react-router-dom";
```

---

Wrap application:

```jsx
<BrowserRouter>

 <App />

</BrowserRouter>
```

---

Usually:

```jsx
main.jsx
```

or

```jsx
index.js
```

---

# Internal Working

BrowserRouter uses:

```text
HTML5 History API
```

---

Methods:

```text
pushState()

replaceState()

popState()
```

---

Allows URL changes without refresh.

---

Visualization:

```text
BrowserRouter
      ↓

Listens To URL Changes
      ↓

Displays Matching Route
```

---

# Routes

---

# 4. What is Routes?

Routes is a container.

---

Holds:

```text
Multiple Route Components
```

---

Example:

```jsx
<Routes>

 <Route />

 <Route />

 <Route />

</Routes>
```

---

Think of it as:

```text
Route Manager
```

---

# Route

---

# 5. What is Route?

Defines:

```text
URL Path
```

and

```text
Component To Render
```

---

Example:

```jsx
<Route

 path="/"

 element={<Home/>}

/>
```

---

Meaning:

```text
URL "/"
```

renders:

```jsx
<Home />
```

---

# Multiple Routes

```jsx
<Routes>

 <Route

  path="/"

  element={<Home/>}

 />

 <Route

  path="/about"

  element={<About/>}

 />

 <Route

  path="/contact"

  element={<Contact/>}

 />

</Routes>
```

---

Visualization:

```text
/

Home

------------

/about

About

------------

/contact

Contact
```

---

# Route Matching

---

# 6. How Route Matching Works

URL:

```text
/about
```

---

React Router:

```text
Checks Routes
```

---

Finds:

```jsx
path="/about"
```

---

Renders:

```jsx
<About />
```

---

# Link

---

# 7. What is Link?

Used for navigation.

---

HTML:

```html
<a href="/about">
 About
</a>
```

---

Problem:

```text
Full Page Reload
```

---

React Router:

```jsx
<Link to="/about">
 About
</Link>
```

---

Benefits:

```text
No Reload

Fast Navigation

SPA Behavior
```

---

# Example

```jsx
<Link to="/">
 Home
</Link>
```

---

```jsx
<Link to="/products">
 Products
</Link>
```

---

```jsx
<Link to="/cart">
 Cart
</Link>
```

---

# Internal Flow

```text
Link Click
     ↓

URL Changes
     ↓

React Router
Matches Route
     ↓

Component Changes
```

---

No refresh.

---

# NavLink

---

# 8. What is NavLink?

Special version of Link.

---

Adds:

```text
Active Route Styling
```

---

Example:

```jsx
<NavLink

 to="/home"

>

 Home

</NavLink>
```

---

Current URL:

```text
/home
```

---

NavLink knows:

```text
This Route Is Active
```

---

# Example

```jsx
<NavLink

 to="/home"

 className={({isActive}) =>

  isActive
   ? "active"
   : ""

 }

>

 Home

</NavLink>
```

---

Active route gets:

```text
active class
```

---

Perfect for:

```text
Navigation Menus
```

---

# useNavigate

---

# 9. What is useNavigate?

Programmatic navigation.

---

Used when:

```text
Navigation Happens
Through JavaScript
```

---

Example:

```jsx
const navigate =

useNavigate();
```

---

Navigate:

```jsx
navigate("/home");
```

---

Redirects user.

---

# Login Example

```jsx
function login() {

 navigate("/dashboard");

}
```

---

Flow:

```text
Login Success
      ↓

Navigate
      ↓

Dashboard
```

---

# Go Back

```jsx
navigate(-1);
```

---

Equivalent:

```text
Browser Back Button
```

---

# Go Forward

```jsx
navigate(1);
```

---

Browser forward.

---

# useParams

---

# 10. What is useParams?

Reads:

```text
Route Parameters
```

---

Route:

```jsx
<Route

 path="/user/:id"

 element={<User/>}

/>
```

---

URL:

```text
/user/101
```

---

Read parameter:

```jsx
const { id } =
useParams();
```

---

Output:

```text
101
```

---

# Visualization

```text
/user/101
      ↓

id = 101
```

---

# Multiple Params

```jsx
<Route

 path="/user/:id/order/:orderId"

 element={<Order/>}

/>
```

---

URL:

```text
/user/101/order/500
```

---

Read:

```jsx
const {

 id,

 orderId

}
=
useParams();
```

---

Output:

```text
101

500
```

---

# useLocation

---

# 11. What is useLocation?

Provides current URL information.

---

Example:

```jsx
const location =
useLocation();
```

---

Properties:

```text
pathname

search

hash

state
```

---

# Example

Current URL:

```text
/products
```

---

```jsx
location.pathname
```

---

Output:

```text
/products
```

---

# Query Parameters Example

URL:

```text
/products?page=2
```

---

Read:

```jsx
location.search
```

---

Output:

```text
?page=2
```

---

# Nested Routing

---

# 12. What is Nested Routing?

Route inside another route.

---

Example:

```text
Dashboard
```

Contains:

```text
Profile

Orders

Settings
```

---

Structure:

```text
/dashboard
         ↓

profile

orders

settings
```

---

# Route Definition

```jsx
<Route

 path="/dashboard"

 element={<Dashboard/>}

>

 <Route

  path="profile"

  element={<Profile/>}

 />

 <Route

  path="orders"

  element={<Orders/>}

 />

</Route>
```

---

# Parent Component

Need:

```jsx
<Outlet />
```

---

Example:

```jsx
function Dashboard() {

 return (

  <>
   <h1>Dashboard</h1>

   <Outlet />
  </>

 );

}
```

---

Outlet renders:

```text
Child Route
```

---

# Protected Routes

---

# 13. What are Protected Routes?

Some routes require:

```text
Authentication
```

---

Examples:

```text
/orders

/profile

/dashboard
```

---

Only logged-in users can access.

---

# Example

Create wrapper:

```jsx
function ProtectedRoute({

 children

}) {

 const isLoggedIn =
 true;

 return isLoggedIn

  ? children

  : <Navigate
      to="/login"
    />;

}
```

---

Usage:

```jsx
<Route

 path="/profile"

 element={

  <ProtectedRoute>

   <Profile/>

  </ProtectedRoute>

 }

/>
```

---

Flow:

```text
User Logged In
      ↓

Profile

Else
      ↓

Login Page
```

---

# Dynamic Routes

---

# 14. What are Dynamic Routes?

Routes containing:

```text
Variable Data
```

---

Example:

```text
/products/101

/products/102

/products/103
```

---

One route handles all.

---

Definition:

```jsx
<Route

 path="/products/:id"

 element={<Product/>}

/>
```

---

Dynamic value:

```text
id
```

---

# Route Parameters

---

# 15. What are Route Parameters?

Values embedded in URL.

---

Example:

```text
/user/101
```

---

Parameter:

```text
101
```

---

Route:

```jsx
/user/:id
```

---

Read:

```jsx
const { id } =
useParams();
```

---

Very common.

---

Examples:

```text
Product Id

User Id

Order Id

Category Id
```

---

# Real World Example

```text
/products/555
```

---

Read:

```jsx
const { id } =
useParams();
```

---

API Call:

```jsx
fetch(

 `/api/products/${id}`

);
```

---

Loads product.

---

# Lazy Loaded Routes

---

# 16. Why Lazy Loading?

Suppose application has:

```text
Home

Products

Orders

Profile

Admin

Reports
```

---

Without lazy loading:

```text
Everything Downloads
Initially
```

---

Large bundle.

---

Slow startup.

---

Need:

```text
Load Only When Needed
```

---

# React.lazy()

---

Example:

```jsx
import {

 lazy

}
from "react";
```

---

```jsx
const Profile =

lazy(() =>

 import("./Profile")

);
```

---

Component loads only when visited.

---

# Suspense

---

Required with lazy loading.

---

Example:

```jsx
<Suspense

 fallback={<h1>

 Loading...

 </h1>}

>

 <Profile/>

</Suspense>
```

---

While downloading:

```text
Loading...
```

---

Shown.

---

# Lazy Route Example

```jsx
const Dashboard =

lazy(() =>

 import("./Dashboard")

);
```

---

Route:

```jsx
<Route

 path="/dashboard"

 element={

 <Suspense
  fallback={<h1>Loading</h1>}
 >

  <Dashboard/>

 </Suspense>

 }

/>
```

---

# Benefits

```text
Smaller Bundle

Faster Initial Load

Better Performance
```

---

# React Router Architecture

---

# 17. Internal Working

Flow:

```text
BrowserRouter
      ↓

Listens URL
      ↓

Routes
      ↓

Route Matching
      ↓

Component Render
```

---

React Router keeps:

```text
Route Configuration
```

---

Matches current URL.

---

Renders corresponding component.

---

# Common Mistakes

---

# 18. Using Anchor Tags

Wrong:

```html
<a href="/about">
 About
</a>
```

---

Causes:

```text
Page Reload
```

---

Use:

```jsx
<Link />
```

---

# 19. Forgetting BrowserRouter

Wrong:

```jsx
<App />
```

---

Without:

```jsx
<BrowserRouter>
```

---

Routing won't work.

---

# 20. Missing Outlet

Nested routes require:

```jsx
<Outlet />
```

---

Without it:

```text
Child Routes
Not Rendered
```

---

# Best Practices

---

# 21. Use NavLink For Menus

Provides:

```text
Active Styling
```

---

Perfect for navigation bars.

---

# 22. Protect Sensitive Routes

Examples:

```text
Profile

Orders

Admin
```

---

Use:

```text
Protected Routes
```

---

# 23. Lazy Load Large Pages

Examples:

```text
Dashboard

Admin

Reports
```

---

Improves performance.

---

# 24. Use Dynamic Routes For IDs

Examples:

```text
Products

Orders

Users
```

---

Avoid creating:

```text
Hundreds Of Routes
```

---

# Interview Questions

---

### Q1. What is Routing?

```text
Showing Components
Based On URL
```

---

### Q2. What is React Router?

```text
Official Routing Library
For React
```

---

### Q3. Why use BrowserRouter?

```text
Enables Routing
```

---

### Q4. Difference Between Route and Routes?

Routes:

```text
Container
```

---

Route:

```text
Single Route
```

---

### Q5. Link vs Anchor Tag?

Link:

```text
No Page Reload
```

---

Anchor:

```text
Page Reload
```

---

### Q6. NavLink vs Link?

NavLink:

```text
Supports Active State
```

---

### Q7. What does useNavigate do?

```text
Programmatic Navigation
```

---

### Q8. What does useParams return?

```text
Route Parameters
```

---

### Q9. What is Nested Routing?

```text
Route Inside Route
```

---

### Q10. Why Lazy Load Routes?

```text
Improve Performance
```

---

# Memory Tricks

### BrowserRouter

```text
Enable Routing
```

---

### Routes

```text
Route Container
```

---

### Route

```text
URL → Component
```

---

### Link

```text
Navigate
```

---

### NavLink

```text
Navigate + Active Styling
```

---

### useNavigate

```text
Navigate Using JavaScript
```

---

### useParams

```text
Read URL Parameters
```

---

### useLocation

```text
Read Current URL
```

---

### Outlet

```text
Render Child Route
```

---

### Protected Route

```text
Authentication Check
```

---

### Lazy Route

```text
Load When Needed
```

---

# Complete Routing Flow

```text
BrowserRouter
        ↓

Routes
        ↓

Route Matching
        ↓

Component Render

Navigation:
Link / NavLink

Programmatic:
useNavigate

URL Params:
useParams

Current URL:
useLocation

Nested Routes:
Outlet

Security:
Protected Routes

Performance:
Lazy Loaded Routes
```

---

# Quick Revision

```text
React Router
------------

Purpose:
Client Side Routing

Core Components:

BrowserRouter
      ↓

Routes
      ↓

Route

Navigation:

Link

NavLink

Hooks:

useNavigate

useParams

useLocation

Advanced:

Nested Routing

Protected Routes

Dynamic Routes

Route Parameters

Lazy Loaded Routes

Remember:

URL
 ↓

Route Match
 ↓

Component Render

No Page Reload
```

---