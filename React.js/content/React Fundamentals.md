# React Fundamentals - Chapter 1

> **Goal:** Understand what React is, why React was created, what problems it solves, its core features, how it works internally, real-world examples, architecture overview, interview questions, advantages, disadvantages, and best practices.

---

# 1. What is React?

React is a JavaScript library used for building User Interfaces (UI).

It was developed by:

```text
Meta (Facebook)
```

and released in:

```text
2013
```

---

React helps developers build:

```text
Interactive
Reusable
Dynamic
Fast
Web Applications
```

---

Examples:

```text
Facebook
Instagram
Netflix
Airbnb
WhatsApp Web
```

---

Instead of manually updating HTML:

```javascript
document.getElementById("title").innerText =
    "Welcome User";
```

React automatically updates the UI when data changes.

---

# 2. Official Definition

> React is a declarative, component-based JavaScript library for building user interfaces.

---

Important keywords:

```text
Declarative
Component-Based
Reusable
Virtual DOM
Efficient Rendering
```

---

# 3. Simple Meaning

Imagine building a house.

Without React:

```text
Build every wall manually
Build every room manually
Manage every change manually
```

---

With React:

```text
Create reusable room templates

Room Component
Kitchen Component
Door Component
Window Component
```

---

Reuse them everywhere.

---

This is exactly how React works.

---

# 4. Problem Before React

Before React, developers used:

```text
HTML
CSS
JavaScript
jQuery
```

---

Suppose a counter exists.

HTML:

```html
<h1 id="count">0</h1>

<button onclick="increment()">
    Increment
</button>
```

---

JavaScript:

```javascript
let count = 0;

function increment() {

    count++;

    document.getElementById("count")
        .innerText = count;
}
```

---

Every UI update requires:

```text
Finding DOM element
Changing DOM
Managing UI manually
```

---

Problems:

```text
Complex Code
Poor Maintainability
Hard Debugging
Slow DOM Manipulation
```

---

Need something better.

---

# 5. React Solution

React introduced:

```text
Component-Based Architecture
```

---

Instead of:

```text
Manipulate DOM Directly
```

React says:

```text
Describe UI

React will update DOM
```

---

Example:

```jsx
function Counter() {

    const [count, setCount] = useState(0);

    return (

        <button
            onClick={() =>
                setCount(count + 1)}>

            {count}

        </button>
    );
}
```

---

Notice:

```text
No DOM manipulation
No getElementById()
No innerHTML()
```

---

React handles everything.

---

# 6. Why Was React Created?

Facebook faced a problem.

---

Large applications had:

```text
Thousands of UI elements
Constant Updates
Frequent User Interactions
```

---

Example:

```text
Notifications
Messages
Likes
Comments
Feeds
```

---

Updating DOM repeatedly became slow.

---

Facebook engineers wanted:

```text
Reusable UI
Fast Rendering
Better Performance
Maintainable Code
```

---

Result:

```text
React
```

---

# 7. Core Philosophy of React

React follows:

```text
UI = f(State)
```

Meaning:

```text
User Interface
depends on
Application State
```

---

Example:

State:

```javascript
const user = "John";
```

---

UI:

```jsx
<h1>Hello John</h1>
```

---

State changes:

```javascript
const user = "David";
```

---

UI automatically changes:

```jsx
<h1>Hello David</h1>
```

---

React handles synchronization.

---

# 8. React is Declarative

One of the most important interview topics.

---

# Imperative Programming

Tell computer:

```text
HOW to do something
```

Example:

```javascript
const element =
document.getElementById("title");

element.innerText = "Hello";
```

---

Step by step instructions.

---

# Declarative Programming

Tell computer:

```text
WHAT you want
```

Example:

```jsx
<h1>Hello</h1>
```

---

React decides:

```text
How to render
How to update
How to optimize
```

---

# 9. React is Component-Based

Everything in React is a component.

---

Examples:

```text
Navbar
Sidebar
Footer
Profile
Product Card
```

---

Visual Representation:

```text
App
│
├── Navbar
│
├── Sidebar
│
├── ProductList
│      │
│      ├── ProductCard
│      ├── ProductCard
│      └── ProductCard
│
└── Footer
```

---

Each piece is reusable.

---

# 10. First React Component

```jsx
function Welcome() {

    return (

        <h1>
            Welcome To React
        </h1>

    );
}
```

---

Usage:

```jsx
<Welcome />
```

---

Output:

```html
<h1>Welcome To React</h1>
```

---

# 11. Why React?

This is one of the most asked interview questions.

---

# Reason 1: Reusable Components

Without React:

```html
Header
Header
Header
Header
```

Repeated everywhere.

---

With React:

```jsx
<Header />
```

Reuse infinitely.

---

Benefits:

```text
Less Code
Easy Maintenance
Better Readability
```

---

# Reason 2: Faster UI Updates

React updates only required parts.

---

Instead of:

```text
Reload Entire Page
```

React:

```text
Updates Changed Component Only
```

---

Performance improves significantly.

---

# Reason 3: Virtual DOM

Traditional DOM updates are expensive.

---

React uses:

```text
Virtual DOM
```

to minimize updates.

---

Result:

```text
Faster Rendering
Better Performance
```

---

# Reason 4: Better Developer Experience

React provides:

```text
Reusable Components
Hooks
State Management
Large Ecosystem
```

---

Development becomes easier.

---

# Reason 5: Huge Community

React is used by:

```text
Meta
Netflix
Airbnb
Uber
Dropbox
Pinterest
```

---

Benefits:

```text
Tutorials
Libraries
Community Support
Jobs
```

---

# Reason 6: Easy Learning Curve

Compared to Angular:

```text
React is easier
```

because React focuses mainly on:

```text
UI Layer
```

---

# Reason 7: Rich Ecosystem

React ecosystem includes:

```text
React Router
Redux
Next.js
React Query
Material UI
Tailwind CSS
```

---

# 12. Features of React

---

# Feature 1: Component-Based Architecture

Build application using components.

---

Example:

```jsx
function Header() {

    return <h1>Header</h1>;
}

function Footer() {

    return <h1>Footer</h1>;
}
```

---

App:

```jsx
function App() {

    return (

        <>
            <Header />
            <Footer />
        </>
    );
}
```

---

# Feature 2: Virtual DOM

React creates:

```text
Virtual Copy Of DOM
```

---

Before updating:

```text
Compare Old Version
Compare New Version
```

---

Update only differences.

---

# Feature 3: JSX

React allows writing:

```jsx
<h1>Hello</h1>
```

inside JavaScript.

---

Makes UI code readable.

---

Example:

```jsx
const element =
<h1>Hello React</h1>;
```

---

# Feature 4: One-Way Data Flow

Data moves:

```text
Parent
 ↓
Child
```

---

Example:

```jsx
function Child(props) {

    return <h1>{props.name}</h1>;
}
```

---

Parent:

```jsx
<Child name="John" />
```

---

Benefits:

```text
Predictable Data Flow
Easy Debugging
```

---

# Feature 5: State Management

React tracks data changes.

---

Example:

```jsx
const [count, setCount] =
useState(0);
```

---

State change:

```jsx
setCount(5);
```

---

UI updates automatically.

---

# Feature 6: Hooks

Hooks allow state and lifecycle features.

---

Examples:

```text
useState
useEffect
useContext
useMemo
useCallback
useReducer
```

---

Example:

```jsx
useEffect(() => {

    console.log("Mounted");

}, []);
```

---

# Feature 7: Fast Rendering

Because of:

```text
Virtual DOM
Diffing Algorithm
Fiber Architecture
```

---

React performs optimized rendering.

---

# Feature 8: Cross Platform

React ecosystem includes:

```text
React Web
React Native
```

---

Write apps for:

```text
Web
Android
iOS
```

---

# Feature 9: Unidirectional Data Flow

Flow:

```text
Parent
 ↓
Child
```

---

Never:

```text
Child
 ↓
Parent
```

directly.

---

Provides:

```text
Better Predictability
```

---

# Feature 10: Strong Ecosystem

React integrates with:

```text
Redux
Next.js
Node.js
Spring Boot
GraphQL
TypeScript
```

---

# 13. Real-World Example

Suppose building Amazon.

Components:

```text
Navbar
SearchBar
ProductList
Cart
Footer
```

---

Structure:

```text
App
│
├── Navbar
├── SearchBar
├── ProductList
│      │
│      ├── ProductCard
│      ├── ProductCard
│      └── ProductCard
│
├── Cart
│
└── Footer
```

---

Reusable:

```text
ProductCard
```

can be used hundreds of times.

---

# 14. Advantages of React

---

## 1. Reusable Components

```text
Write Once
Reuse Everywhere
```

---

## 2. Better Performance

Virtual DOM optimization.

---

## 3. Easier Maintenance

Components are isolated.

---

## 4. Large Community

Massive ecosystem.

---

## 5. SEO Support

Using:

```text
Next.js
```

---

## 6. Strong Job Market

React developers are highly demanded.

---

# 15. Disadvantages of React

---

## 1. Rapid Changes

React ecosystem evolves frequently.

---

## 2. Requires Additional Libraries

Need separate libraries for:

```text
Routing
State Management
Forms
```

---

## 3. JSX Learning Curve

HTML inside JavaScript may initially feel strange.

---

## 4. Frontend Only

React mainly handles UI.

Backend still required.

---

# 16. React Workflow

```text
User Action
      ↓

State Changes
      ↓

React Creates New Virtual DOM
      ↓

Diffing
      ↓

Find Changes
      ↓

Update Real DOM
      ↓

Browser Repaint
```

---

# 17. Common Interview Questions

### Q1. What is React?

```text
A JavaScript library used for building user interfaces using reusable components.
```

---

### Q2. Why React is fast?

```text
Virtual DOM
Diffing Algorithm
Fiber Architecture
```

---

### Q3. Is React a framework?

```text
No
```

React is a:

```text
Library
```

---

### Q4. Who developed React?

```text
Meta (Facebook)
```

---

### Q5. What is Component-Based Architecture?

Breaking UI into reusable independent components.

---

### Q6. What is Declarative Programming?

Describe:

```text
What UI should look like
```

instead of:

```text
How to update UI
```

---

### Q7. What are major features of React?

```text
Components
Virtual DOM
JSX
Hooks
One-Way Data Binding
Fast Rendering
Reusable UI
```

---

### Q8. Why is React popular?

```text
Easy Learning
Performance
Reusable Components
Huge Ecosystem
Industry Adoption
```

---

# 18. Memory Tricks

### React

```text
Build UI Using Components
```

---

### Component

```text
Reusable UI Block
```

---

### State

```text
Data That Changes
```

---

### Props

```text
Data Passed To Component
```

---

### JSX

```text
HTML Inside JavaScript
```

---

### Virtual DOM

```text
DOM Copy For Fast Updates
```

---

### React Philosophy

```text
UI = f(State)
```

---

# Quick Revision

```text
React
-----

Created By:
Meta (Facebook)

Released:
2013

Purpose:
Build User Interfaces

Core Concepts:

1. Components
2. State
3. Props
4. JSX
5. Virtual DOM
6. One-Way Data Flow
7. Hooks

Why React?

✔ Reusable Components
✔ Fast Rendering
✔ Better Maintainability
✔ Huge Ecosystem
✔ Easy Learning

Major Features

✔ Component-Based
✔ Virtual DOM
✔ JSX
✔ Hooks
✔ State Management
✔ One-Way Data Flow
✔ Fast Updates

Formula:

UI = f(State)

Meaning:

UI changes automatically
when State changes.
```

---
# React Fundamentals - Chapter 2

> **Goal:** Understand Single Page Applications (SPA), React vs Angular vs Vue, React Architecture, internal working, routing, rendering flow, real-world examples, advantages, disadvantages, interview questions, and architecture diagrams.

---

# 1. Single Page Application (SPA)

---

# What is SPA?

SPA stands for:

```text
Single Page Application
```

A web application that loads:

```text
Only One HTML Page
```

and dynamically updates content without reloading the entire page.

---

Traditional websites:

```text
Every click
      ↓
Request Server
      ↓
Get New HTML Page
      ↓
Full Page Reload
```

---

SPA:

```text
Load Once
      ↓
Update Content Dynamically
      ↓
No Full Page Reload
```

---

# Traditional Multi-Page Application (MPA)

Examples:

```text
Older Banking Websites
Government Portals
Traditional JSP Applications
```

---

Flow:

```text
Browser
   ↓
Request /home
   ↓
Server Returns HTML

Browser
   ↓
Request /products
   ↓
Server Returns New HTML

Browser
   ↓
Request /cart
   ↓
Server Returns New HTML
```

---

Every navigation:

```text
Entire Page Reload
```

---

Problems:

```text
Slow Navigation
Extra Network Requests
Poor User Experience
Higher Server Load
```

---

# SPA Architecture

```text
Browser
   ↓
Loads React Application Once
   ↓
JavaScript Takes Control
   ↓
Changes UI Dynamically
```

---

Visualization:

```text
+--------------------+
| index.html         |
+--------------------+
          |
          v

+--------------------+
| React Application  |
+--------------------+
          |
          v

+--------------------+
| Components         |
+--------------------+
```

---

# SPA Navigation Example

User clicks:

```text
Home
```

---

React displays:

```text
Home Component
```

---

User clicks:

```text
Products
```

---

React displays:

```text
Products Component
```

---

No page reload.

---

Everything happens inside:

```text
Browser Memory
```

---

# Example Using React Router

```jsx
import {
 BrowserRouter,
 Routes,
 Route
} from "react-router-dom";

function App() {

 return (

  <BrowserRouter>

   <Routes>

    <Route
      path="/"
      element={<Home />}
    />

    <Route
      path="/products"
      element={<Products />}
    />

   </Routes>

  </BrowserRouter>

 );
}
```

---

Notice:

```text
No Full Page Refresh
```

---

Only components change.

---

# Real World Example

Instagram:

```text
Click Profile
```

---

Only profile content changes.

---

Entire application does not reload.

---

Same for:

```text
Facebook
Twitter
LinkedIn
Gmail
Netflix
```

---

# Advantages of SPA

---

## Faster Navigation

No page reload.

---

## Better User Experience

Feels like:

```text
Desktop Application
```

---

## Reduced Server Load

Server mainly sends:

```text
JSON Data
```

instead of full HTML pages.

---

## Better Caching

JavaScript files loaded once.

---

## Real-Time Interactions

Supports:

```text
Chats
Notifications
Feeds
Dashboards
```

---

# Disadvantages of SPA

---

## SEO Challenges

Search engines traditionally prefer:

```text
Server Rendered HTML
```

---

Solution:

```text
Next.js
Server Side Rendering
```

---

## Initial Loading Time

Large JavaScript bundle.

---

## Browser Memory Usage

Entire application runs in browser.

---

# SPA Flow Diagram

```text
User Click
      ↓

React Router
      ↓

Route Match
      ↓

Component Render
      ↓

Virtual DOM Update
      ↓

Real DOM Update
```

---

# 2. React vs Angular vs Vue

One of the most important interview topics.

---

# Overview

| Feature | React | Angular | Vue |
|----------|---------|---------|---------|
| Type | Library | Framework | Framework |
| Language | JavaScript | TypeScript | JavaScript |
| Learning Curve | Easy | Hard | Easy |
| Performance | Very Fast | Fast | Fast |
| Flexibility | High | Low | Medium |
| Community | Huge | Large | Large |
| Developed By | Meta | Google | Evan You |

---

# React

Developed by:

```text
Meta
```

---

Focus:

```text
UI Layer Only
```

---

Need external libraries for:

```text
Routing
State Management
Forms
```

---

Example:

```text
React Router
Redux
Formik
```

---

Advantages:

```text
Flexible
Large Community
Huge Job Market
```

---

Disadvantages:

```text
Requires Additional Libraries
```

---

# Angular

Developed by:

```text
Google
```

---

Angular is:

```text
Complete Framework
```

---

Provides:

```text
Routing
HTTP Client
Dependency Injection
Forms
Testing
```

Out of the box.

---

Advantages:

```text
Enterprise Ready
Strong Architecture
```

---

Disadvantages:

```text
Steep Learning Curve
More Boilerplate
```

---

Example:

```typescript
@Component({
 selector: 'app-root'
})
```

---

Much more structured.

---

# Vue

Created by:

```text
Evan You
```

---

Combination of:

```text
React Simplicity
+
Angular Features
```

---

Advantages:

```text
Easy Learning
Simple Syntax
Good Performance
```

---

Disadvantages:

```text
Smaller Ecosystem
Fewer Enterprise Jobs
```

---

# Performance Comparison

```text
React
   ≈
Vue
   >
Angular
```

for many UI-heavy applications.

---

Reason:

```text
Virtual DOM
Efficient Rendering
```

---

# Learning Curve

```text
Vue
  ↓
React
  ↓
Angular
```

---

Easiest:

```text
Vue
```

---

Hardest:

```text
Angular
```

---

# Market Demand

Generally:

```text
React
  >
Angular
  >
Vue
```

---

React dominates:

```text
Startups
Product Companies
SaaS Companies
```

---

Angular dominates:

```text
Large Enterprises
Banking
Government Projects
```

---

# Interview Question

Why choose React over Angular?

Answer:

```text
React is lighter,
more flexible,
easier to learn,
and has a larger ecosystem.
```

---

# 3. React Architecture

---

# What is React Architecture?

React Architecture is the structure and organization of:

```text
Components
State
Props
Routing
API Layer
```

inside an application.

---

# High-Level Architecture

```text
User
  ↓

Browser
  ↓

React Application
  ↓

Components
  ↓

Virtual DOM
  ↓

Real DOM
```

---

# Component-Based Architecture

Everything is divided into components.

---

Example:

```text
Amazon
```

---

Components:

```text
Navbar
SearchBar
ProductList
ProductCard
Cart
Footer
```

---

Architecture:

```text
App
│
├── Navbar
│
├── SearchBar
│
├── ProductList
│       │
│       ├── ProductCard
│       ├── ProductCard
│       └── ProductCard
│
├── Cart
│
└── Footer
```

---

# Root Component

Every React application starts from:

```jsx
<App />
```

---

Entry Point:

```jsx
import ReactDOM
from "react-dom/client";

import App from "./App";

ReactDOM
.createRoot(
 document.getElementById("root")
)
.render(<App />);
```

---

Flow:

```text
main.jsx
    ↓

App.jsx
    ↓

Child Components
```

---

# Data Flow Architecture

React follows:

```text
One-Way Data Flow
```

---

Flow:

```text
Parent
   ↓
Child
```

---

Example:

```jsx
function Parent() {

 return (
   <Child
     name="John"
   />
 );
}
```

---

Child:

```jsx
function Child(props) {

 return (
   <h1>
     {props.name}
   </h1>
 );
}
```

---

Flow:

```text
Parent → Child
```

Only.

---

# State Architecture

State lives inside components.

---

Example:

```jsx
const [count, setCount]
= useState(0);
```

---

When state changes:

```jsx
setCount(10);
```

---

React:

```text
Re-renders Component
```

---

# API Layer Architecture

Most React applications:

```text
Frontend
      ↓
REST API
      ↓
Backend
      ↓
Database
```

---

Example:

```text
React
  ↓
Spring Boot
  ↓
MySQL
```

---

# Architecture Diagram

```text
React UI
    │
    ▼

Components
    │
    ▼

State & Props
    │
    ▼

API Calls
    │
    ▼

Backend
    │
    ▼

Database
```

---

# Enterprise React Architecture

```text
src
│
├── components
│
├── pages
│
├── services
│
├── hooks
│
├── routes
│
├── context
│
├── redux
│
├── assets
│
└── utils
```

---

# Example

```text
components/
```

contains:

```text
Button
Navbar
Card
Footer
```

---

```text
pages/
```

contains:

```text
Home
Login
Products
Cart
```

---

```text
services/
```

contains:

```text
API Calls
Axios Configuration
```

---

# React Application Lifecycle

```text
Application Starts
        ↓

App Component Loads
        ↓

Children Components Render
        ↓

State Changes
        ↓

Virtual DOM Updates
        ↓

Diffing
        ↓

Real DOM Updates
```

---

# Advantages of React Architecture

---

## Reusability

Components can be reused.

---

## Scalability

Large applications become manageable.

---

## Maintainability

Changes remain isolated.

---

## Testability

Components can be tested individually.

---

## Better Team Collaboration

Developers can work on different components.

---

# Disadvantages

---

## State Management Complexity

Large applications require:

```text
Redux
Context API
Zustand
```

---

## Component Nesting

Deep hierarchy may cause:

```text
Prop Drilling
```

---

## Folder Structure Decisions

React provides flexibility.

Too much flexibility can create inconsistency.

---

# Interview Questions

---

### Q1. What is SPA?

```text
A web application that loads a single HTML page and dynamically updates content without full page reloads.
```

---

### Q2. Why are SPAs fast?

```text
Only required UI changes are rendered.
No full page reload occurs.
```

---

### Q3. React vs Angular?

```text
React = Library

Angular = Complete Framework
```

---

### Q4. React vs Vue?

```text
React has larger ecosystem.

Vue has simpler syntax.
```

---

### Q5. What architecture does React follow?

```text
Component-Based Architecture
```

---

### Q6. What is React's data flow?

```text
One-Way Data Flow

Parent → Child
```

---

### Q7. What is the root component?

```jsx
<App />
```

---

### Q8. What is React mainly responsible for?

```text
Building User Interfaces
```

---

# Memory Tricks

### SPA

```text
Load Once
Update Many Times
```

---

### React

```text
Flexible UI Library
```

---

### Angular

```text
Complete Framework
```

---

### Vue

```text
Simple Framework
```

---

### React Architecture

```text
Components
    ↓

State
    ↓

Virtual DOM
    ↓

Real DOM
```

---

# Quick Revision

```text
SPA
---

One HTML Page
No Full Reload
Fast Navigation

React vs Angular vs Vue
-----------------------

React:
Library

Angular:
Framework

Vue:
Lightweight Framework

React Architecture
------------------

App
 ↓

Components
 ↓

State / Props
 ↓

Virtual DOM
 ↓

Real DOM

Key Principles
--------------

✔ Component-Based
✔ Reusable UI
✔ One-Way Data Flow
✔ Fast Rendering
✔ Scalable Structure
✔ API Driven Architecture
```

---
**Next Chapter:** Virtual DOM, Real DOM vs Virtual DOM, and React Rendering Process (the most important React internals for interviews).

---
# React Fundamentals - Chapter 3

> **Goal:** Understand Virtual DOM, Real DOM vs Virtual DOM, React Rendering Process, Diffing Algorithm, Reconciliation, Internal Working, Render Phase, Commit Phase, React Optimization Techniques, Interview Questions, and Real-World Examples.

---

# 1. Why Do We Need Virtual DOM?

Before understanding Virtual DOM, we must understand:

```text
What is DOM?
```

---

# 2. What is DOM?

DOM stands for:

```text
Document Object Model
```

---

When a browser loads HTML:

```html
<html>
    <body>
        <h1>Hello</h1>
    </body>
</html>
```

Browser converts it into:

```text
Tree Structure
```

---

Visualization:

```text
Document
   |
 html
   |
 body
   |
 h1
   |
 Hello
```

---

This tree is called:

```text
DOM Tree
```

---

JavaScript can manipulate it.

Example:

```javascript
document.getElementById("title")
```

---

or

```javascript
document.querySelector("h1")
```

---

# 3. Real DOM

The actual DOM maintained by the browser is called:

```text
Real DOM
```

---

Example:

```html
<h1 id="title">Hello</h1>
```

---

JavaScript:

```javascript
document
.getElementById("title")
.innerText = "Welcome";
```

---

Browser updates DOM.

---

Then browser:

```text
Recalculates Layout
Recalculates Styles
Performs Repaint
Performs Reflow
```

---

These operations are expensive.

---

# 4. Problem with Real DOM

Imagine:

```text
Facebook Feed
```

Contains:

```text
1000 Posts
1000 Comments
1000 Likes
```

---

User clicks:

```text
Like Button
```

---

Only one value changes:

```text
10 Likes → 11 Likes
```

---

Traditional DOM updates may trigger:

```text
Layout Calculations
Rendering Work
DOM Traversal
```

---

For large applications:

```text
Performance Problems
```

---

Need optimization.

---

# 5. React Solution

React introduced:

```text
Virtual DOM
```

---

Idea:

```text
Never update Real DOM directly
```

---

Instead:

```text
Update Virtual DOM
Compare Changes
Update Only Necessary Parts
```

---

Result:

```text
Faster Rendering
Better Performance
```

---

# 6. What is Virtual DOM?

Virtual DOM is:

```text
Lightweight JavaScript Object
Representation Of Real DOM
```

---

Example HTML:

```html
<h1>Hello</h1>
```

---

Virtual DOM Object:

```javascript
{
    type: "h1",
    props: {
        children: "Hello"
    }
}
```

---

React stores UI as:

```text
JavaScript Objects
```

instead of actual browser DOM elements.

---

# 7. Real DOM vs Virtual DOM Visualization

Real DOM:

```text
Browser DOM Tree
```

---

Virtual DOM:

```text
JavaScript Memory Tree
```

---

Visualization:

```text
Real DOM

Browser
  |
 html
  |
 body
  |
 div
  |
 h1
```

---

Virtual DOM:

```text
JS Object

{
 type: "div",
 children: [
   {
     type: "h1"
   }
 ]
}
```

---

Virtual DOM exists:

```text
Inside Memory
```

---

Much faster to manipulate.

---

# 8. Virtual DOM Workflow

User action:

```text
Button Click
```

---

State changes:

```javascript
setCount(1);
```

---

React creates:

```text
New Virtual DOM
```

---

Then:

```text
Compare Old Virtual DOM
vs
New Virtual DOM
```

---

Find differences.

---

Update only affected elements.

---

Visualization:

```text
Old Virtual DOM
       |
       |
Compare
       |
       |
New Virtual DOM
       |
Differences
       |
Update Real DOM
```

---

# 9. Example Without React

HTML:

```html
<h1 id="count">0</h1>
```

---

JavaScript:

```javascript
let count = 0;

function increment() {

    count++;

    document
    .getElementById("count")
    .innerText = count;
}
```

---

Developer manually updates DOM.

---

Problems:

```text
Complex
Error Prone
Hard To Maintain
```

---

# 10. Same Example Using React

```jsx
function Counter() {

 const [count, setCount]
 = useState(0);

 return (

  <>
   <h1>{count}</h1>

   <button
    onClick={() =>
      setCount(count + 1)}
   >
     Increment
   </button>
  </>
 );
}
```

---

React automatically:

```text
Creates New Virtual DOM
Finds Difference
Updates Real DOM
```

---

Developer focuses only on:

```text
State
```

---

# 11. Virtual DOM Internal Working

Initial Render:

```jsx
<h1>Hello</h1>
```

---

Virtual DOM:

```javascript
{
 type: "h1",
 children: "Hello"
}
```

---

After state update:

```jsx
<h1>Welcome</h1>
```

---

New Virtual DOM:

```javascript
{
 type: "h1",
 children: "Welcome"
}
```

---

Comparison:

```text
Hello
  ↓
Welcome
```

---

React updates only:

```text
Text Node
```

---

Not entire page.

---

# 12. Reconciliation

One of the most important React interview topics.

---

Definition:

> Reconciliation is the process React uses to compare old and new Virtual DOM trees and determine the minimum number of changes needed.

---

Simple Meaning:

```text
Find What Changed
```

---

Flow:

```text
Old Virtual DOM
      ↓

New Virtual DOM
      ↓

Compare
      ↓

Find Differences
      ↓

Update Real DOM
```

---

This process is called:

```text
Reconciliation
```

---

# 13. Diffing Algorithm

During reconciliation React performs:

```text
Diffing
```

---

Diffing means:

```text
Comparing Two Trees
```

---

Example:

Old:

```html
<h1>Hello</h1>
```

---

New:

```html
<h1>Welcome</h1>
```

---

React identifies:

```text
Only Text Changed
```

---

Update:

```text
Text Node Only
```

---

No need to recreate:

```text
h1 Element
```

---

# 14. React Diffing Assumptions

React uses two assumptions.

---

# Assumption 1

Different element types:

```html
<div>
```

to

```html
<section>
```

means:

```text
Completely Different Tree
```

---

React destroys old tree.

---

Creates new tree.

---

# Assumption 2

Keys identify elements.

---

Example:

```jsx
users.map(user =>

 <User
   key={user.id}
 />
)
```

---

Keys help React identify:

```text
Added Elements
Removed Elements
Moved Elements
```

---

Very important.

---

# 15. Why Keys Matter

Without keys:

```jsx
<li>A</li>
<li>B</li>
<li>C</li>
```

---

Add new item at beginning:

```jsx
<li>X</li>
<li>A</li>
<li>B</li>
<li>C</li>
```

---

React may unnecessarily update:

```text
A
B
C
```

---

With keys:

```jsx
<li key="1">A</li>
<li key="2">B</li>
<li key="3">C</li>
```

---

React understands:

```text
Only X Added
```

---

Performance improves.

---

# 16. React Rendering Process

One of the most asked interview questions.

---

Complete Flow:

```text
User Action
      ↓

State Changes
      ↓

React Starts Render
      ↓

Create New Virtual DOM
      ↓

Diffing
      ↓

Reconciliation
      ↓

Update Real DOM
      ↓

Browser Paint
```

---

# 17. Detailed Rendering Flow

Example:

```jsx
setCount(count + 1);
```

---

Step 1:

```text
State Changes
```

---

Step 2:

```text
React Marks Component Dirty
```

---

Step 3:

```text
Re-render Component
```

---

Step 4:

```text
Create New Virtual DOM
```

---

Step 5:

```text
Compare Trees
```

---

Step 6:

```text
Generate DOM Updates
```

---

Step 7:

```text
Apply Changes To Real DOM
```

---

Step 8:

```text
Browser Paint
```

---

# 18. Initial Render

Application starts:

```jsx
<App />
```

---

React:

```text
Creates Virtual DOM
```

---

Then:

```text
Creates Real DOM
```

---

Then:

```text
Displays UI
```

---

Visualization:

```text
JSX
  ↓

Virtual DOM
  ↓

Real DOM
  ↓

Screen
```

---

# 19. Re-rendering

Re-render occurs when:

---

## State Changes

```jsx
setCount(10);
```

---

## Props Change

Parent passes new props.

---

## Context Changes

Context value updated.

---

## Force Update

Rare case.

---

# 20. Render Phase

React rendering has two phases.

---

Phase 1:

```text
Render Phase
```

---

Purpose:

```text
Calculate Changes
```

---

Tasks:

```text
Create New Virtual DOM
Run Components
Build Tree
Compare Trees
```

---

No DOM updates yet.

---

# 21. Commit Phase

Phase 2:

```text
Commit Phase
```

---

Purpose:

```text
Apply Changes
```

---

Tasks:

```text
Update Real DOM
Run Effects
Paint Screen
```

---

Visualization:

```text
Render Phase
      ↓
Calculate Changes

Commit Phase
      ↓
Apply Changes
```

---

# 22. Example Rendering Lifecycle

Component:

```jsx
function Counter() {

 const [count, setCount]
 = useState(0);

 return (
   <h1>{count}</h1>
 );
}
```

---

User clicks:

```text
Increment
```

---

Flow:

```text
setCount()
      ↓

Render Phase
      ↓

New Virtual DOM
      ↓

Diffing
      ↓

Commit Phase
      ↓

DOM Update
```

---

# 23. Performance Benefits of Virtual DOM

Without Virtual DOM:

```text
Update Real DOM Directly
```

---

Every change expensive.

---

With Virtual DOM:

```text
Batch Updates
Compare Trees
Update Minimum Changes
```

---

Benefits:

```text
Faster Rendering
Better Performance
Less Browser Work
```

---

# 24. Common Misconception

Many people say:

```text
Virtual DOM is faster than DOM
```

---

Not exactly.

---

Correct statement:

```text
Manipulating Virtual DOM
is faster than repeatedly
manipulating Real DOM.
```

---

The optimization comes from:

```text
Diffing
Batching
Selective Updates
```

---

# 25. Real-World Example

Instagram Feed:

```text
1000 Posts
```

---

One post receives:

```text
New Like
```

---

React updates:

```text
Like Count Component
```

Only.

---

Not entire page.

---

# 26. Advantages of Virtual DOM

---

## Faster UI Updates

Only changed elements update.

---

## Better Performance

Less DOM manipulation.

---

## Easier Programming

Developer focuses on state.

---

## Cross Platform

Virtual DOM concept works with:

```text
React Web
React Native
```

---

# 27. Disadvantages

---

## Additional Memory Usage

Stores Virtual DOM tree.

---

## Learning Complexity

Internal working not straightforward.

---

## Not Always Faster

Small applications may not see significant benefits.

---

# 28. Interview Questions

---

### Q1. What is DOM?

```text
Document Object Model
```

Browser representation of HTML.

---

### Q2. What is Virtual DOM?

```text
JavaScript representation
of Real DOM.
```

---

### Q3. Why does React use Virtual DOM?

```text
To minimize expensive
Real DOM operations.
```

---

### Q4. What is Reconciliation?

```text
Process of comparing
old and new Virtual DOM.
```

---

### Q5. What is Diffing?

```text
Comparing two Virtual DOM trees.
```

---

### Q6. What triggers re-render?

```text
State Change
Props Change
Context Change
```

---

### Q7. What are React's Diffing assumptions?

```text
Different Types
→ Different Trees

Keys
→ Stable Identity
```

---

### Q8. Why are keys important?

```text
Help React identify
which items changed.
```

---

### Q9. What are Render and Commit phases?

```text
Render:
Calculate Changes

Commit:
Apply Changes
```

---

### Q10. Is Virtual DOM faster than Real DOM?

Correct answer:

```text
No.

Virtual DOM helps React
perform fewer Real DOM updates.
```

---

# Memory Tricks

### DOM

```text
Browser Tree
```

---

### Virtual DOM

```text
JavaScript Tree
```

---

### Diffing

```text
Compare Trees
```

---

### Reconciliation

```text
Find Changes
```

---

### Render Phase

```text
Calculate
```

---

### Commit Phase

```text
Apply
```

---

# Quick Revision

```text
Virtual DOM
-----------

Lightweight JavaScript
representation of Real DOM.

Workflow
--------

State Change
      ↓

New Virtual DOM
      ↓

Diffing
      ↓

Reconciliation
      ↓

Real DOM Update

Important Terms
---------------

DOM
Virtual DOM
Diffing
Reconciliation
Render Phase
Commit Phase

Advantages
----------

✔ Faster Updates
✔ Better Performance
✔ Less DOM Manipulation
✔ Easier UI Development

Interview Keywords
------------------

Virtual DOM
Diffing
Reconciliation
Keys
Render Phase
Commit Phase
Selective Updates
```

---

# What's Next?

Chapter 4 will cover:

```text
React Fiber Architecture
JSX Introduction
JSX Rules
```

This is where React internals become much deeper and interview-focused, especially:

✔ React Fiber
✔ Incremental Rendering
✔ Scheduling
✔ Priorities
✔ Concurrent Rendering
✔ JSX Compilation
✔ JSX Transformation

---
# React Fundamentals - Chapter 4

> **Goal:** Understand React Fiber Architecture, why Fiber was introduced, how React scheduling works, incremental rendering, priorities, concurrent rendering basics, JSX Introduction, JSX Rules, JSX Compilation Process, JSX Internals, and interview questions.

---

# 1. Why React Fiber Was Created?

Before React 16:

```text
React Stack Reconciler
```

was used.

---

React processed rendering:

```text
Synchronously
```

Meaning:

```text
Start Rendering
      ↓
Finish Rendering
      ↓
Return Control To Browser
```

---

Problem:

Large applications could block the browser.

---

Example:

```text
1000 Components
```

Need re-render.

---

Old React:

```text
Render Component 1
Render Component 2
Render Component 3
...
Render Component 1000
```

---

Until completion:

```text
Browser Cannot Respond
```

---

Problems:

```text
UI Lag
Input Delay
Poor User Experience
Janky Animations
```

---

Need a better solution.

---

# 2. React Fiber

React Fiber was introduced in:

```text
React 16
```

---

Definition:

> React Fiber is a complete rewrite of React's reconciliation engine that enables incremental rendering, scheduling, prioritization, and interruption of rendering work.

---

Simple Meaning:

Instead of:

```text
Finish Everything First
```

React Fiber says:

```text
Do Small Work Chunks
Pause If Needed
Resume Later
```

---

# 3. Real-Life Example

Without Fiber:

```text
Finish Entire Homework
Then Eat Food
```

---

With Fiber:

```text
Do Homework
Pause

Eat Food

Resume Homework
```

---

Much more flexible.

---

# 4. Core Idea Behind Fiber

Old React:

```text
One Big Task
```

---

Fiber:

```text
Many Small Tasks
```

---

Visualization:

```text
Large Work
     ↓

Small Unit
Small Unit
Small Unit
Small Unit
```

---

React can:

```text
Pause
Resume
Cancel
Prioritize
```

work.

---

# 5. Fiber Architecture Overview

Visualization:

```text
React App
      ↓

Fiber Tree
      ↓

Scheduler
      ↓

Reconciliation
      ↓

Commit
```

---

Fiber acts as:

```text
Work Management System
```

---

# 6. What is a Fiber?

A Fiber is:

```text
JavaScript Object
```

representing:

```text
Component
```

or

```text
DOM Element
```

---

Example Component:

```jsx
function Header() {

 return <h1>Hello</h1>;
}
```

---

React creates:

```text
Fiber Node
```

for:

```text
Header Component
```

---

# 7. Fiber Tree

Suppose:

```jsx
<App />
```

contains:

```jsx
<Navbar />
<ProductList />
<Footer />
```

---

Fiber Tree:

```text
App
 │
 ├── Navbar
 │
 ├── ProductList
 │
 └── Footer
```

---

Every node:

```text
Fiber Object
```

---

# 8. Information Stored Inside Fiber

Fiber stores:

```text
Component Type
Props
State
Effects
Priority
Relationships
```

---

Visualization:

```text
Fiber Node

Component
State
Props
Priority
Child
Sibling
Parent
```

---

# 9. Fiber Node Structure (Simplified)

```javascript
const fiber = {

 type: Component,

 stateNode: null,

 child: null,

 sibling: null,

 return: null,

 pendingProps: {},

 memoizedProps: {},

 memoizedState: {}
};
```

---

Interview Point:

```text
Fiber is just a JavaScript object.
```

---

# 10. Fiber Relationships

Each Fiber contains:

---

## Child

```text
First Child
```

---

## Sibling

```text
Next Component
```

---

## Return

```text
Parent
```

---

Visualization:

```text
App
 │
 ├── Navbar
 ├── ProductList
 └── Footer
```

---

Connections:

```text
Navbar
  |
Sibling
  |
ProductList
  |
Sibling
  |
Footer
```

---

# 11. Incremental Rendering

Most important Fiber feature.

---

Old React:

```text
Render Entire Tree
```

---

Fiber:

```text
Render Small Pieces
```

---

Visualization:

```text
Task
 ↓

Chunk 1

Chunk 2

Chunk 3

Chunk 4
```

---

Browser remains responsive.

---

# 12. Scheduling

React Fiber introduced:

```text
Scheduler
```

---

Purpose:

```text
Determine Which Work
Should Run First
```

---

Example:

```text
Typing In Input
```

should be faster than:

```text
Rendering Hidden List
```

---

React prioritizes:

```text
User Input
```

---

# 13. Priority Levels

Conceptually:

```text
High Priority
Medium Priority
Low Priority
```

---

Example:

High Priority:

```text
Typing
Clicking
Scrolling
```

---

Low Priority:

```text
Background Rendering
Analytics Updates
```

---

# 14. Interruptible Rendering

Huge React Innovation.

---

Old React:

```text
Cannot Stop Rendering
```

---

Fiber:

```text
Can Pause Rendering
```

---

Example:

```text
Rendering List
```

---

User clicks:

```text
Search
```

---

React:

```text
Pause List Rendering

Handle Search

Resume Rendering
```

---

# 15. Concurrent Rendering Concept

React can:

```text
Prepare New UI
Without Blocking Current UI
```

---

Current Screen:

```text
Visible
```

---

New Screen:

```text
Prepared In Background
```

---

Then swapped.

---

User experiences:

```text
Smooth UI
```

---

# 16. Render Phase vs Commit Phase

Fiber uses two phases.

---

# Render Phase

Purpose:

```text
Calculate Changes
```

---

Tasks:

```text
Create Fiber Tree
Run Components
Compare Trees
Prepare Updates
```

---

Can be:

```text
Paused
Interrupted
Restarted
```

---

# Commit Phase

Purpose:

```text
Apply Changes
```

---

Tasks:

```text
Update DOM
Run Effects
Paint UI
```

---

Cannot be interrupted.

---

Visualization:

```text
Render Phase
      ↓

Calculate Work
      ↓

Commit Phase
      ↓

Apply Work
```

---

# 17. Why Fiber Is Important?

Fiber enables:

```text
Concurrent Rendering
Suspense
Transitions
Time Slicing
Prioritization
```

---

Without Fiber:

```text
Modern React Impossible
```

---

# 18. JSX Introduction

Now let's understand JSX.

---

# What is JSX?

JSX stands for:

```text
JavaScript XML
```

---

Allows writing:

```html
HTML-like Syntax
```

inside JavaScript.

---

Example:

```jsx
const element =

<h1>Hello React</h1>;
```

---

Looks like:

```html
HTML
```

but it is actually:

```text
JavaScript Syntax Extension
```

---

# 19. Why JSX Was Created?

Without JSX:

```javascript
React.createElement(
 "h1",
 null,
 "Hello React"
);
```

---

Hard to read.

---

With JSX:

```jsx
<h1>Hello React</h1>
```

---

Much cleaner.

---

# 20. JSX Example

```jsx
function App() {

 return (

  <div>

   <h1>
     Welcome
   </h1>

  </div>

 );
}
```

---

Output:

```html
<div>
 <h1>Welcome</h1>
</div>
```

---

# 21. JSX Is NOT HTML

Very important interview question.

---

Many beginners think:

```text
JSX = HTML
```

---

Wrong.

---

JSX is:

```text
JavaScript Syntax
```

---

Eventually converted into:

```javascript
React.createElement()
```

calls.

---

# 22. JSX Conversion

JSX:

```jsx
<h1>Hello</h1>
```

---

Converted to:

```javascript
React.createElement(
 "h1",
 null,
 "Hello"
);
```

---

React then creates:

```text
Virtual DOM Objects
```

---

# 23. JSX Internals

Flow:

```text
JSX
 ↓

Babel
 ↓

React.createElement()
 ↓

Virtual DOM
 ↓

Real DOM
```

---

Very important interview flow.

---

# 24. JSX Rules

Most asked interview topic.

---

# Rule 1

Return Single Parent Element

Wrong:

```jsx
return (

 <h1>Hello</h1>

 <p>React</p>

);
```

---

Error.

---

Correct:

```jsx
return (

 <div>

  <h1>Hello</h1>

  <p>React</p>

 </div>

);
```

---

Or:

```jsx
return (

 <>

  <h1>Hello</h1>

  <p>React</p>

 </>

);
```

---

# Rule 2

Close Every Tag

Wrong:

```jsx
<input>
```

---

Correct:

```jsx
<input />
```

---

Also:

```jsx
<img />
<br />
<hr />
```

---

# Rule 3

Use className Instead Of class

HTML:

```html
<div class="container">
```

---

JSX:

```jsx
<div className="container">
```

---

Reason:

```text
class is JavaScript keyword.
```

---

# Rule 4

Use htmlFor Instead Of for

HTML:

```html
<label for="name">
```

---

JSX:

```jsx
<label htmlFor="name">
```

---

# Rule 5

JavaScript Uses Curly Braces

Example:

```jsx
const name = "John";
```

---

JSX:

```jsx
<h1>{name}</h1>
```

---

Output:

```text
John
```

---

# Rule 6

Use Camel Case Attributes

Wrong:

```jsx
<button onclick="">
```

---

Correct:

```jsx
<button onClick="">
```

---

Examples:

```jsx
onClick
onChange
onSubmit
```

---

# Rule 7

Style Attribute Uses Object

Wrong:

```jsx
<h1 style="color:red">
```

---

Correct:

```jsx
<h1
 style={{
  color: "red"
 }}
>
```

---

# 25. JSX Fragment

Instead of:

```jsx
<div>
 ...
</div>
```

---

Use:

```jsx
<>
 ...
</>
```

---

Called:

```text
React Fragment
```

---

Benefits:

```text
No Extra DOM Node
Cleaner Structure
```

---

# 26. JSX Comments

Wrong:

```jsx
<!-- Comment -->
```

---

Correct:

```jsx
{
 /* Comment */
}
```

---

Example:

```jsx
return (

 <div>

  {/* User Name */}

  <h1>John</h1>

 </div>

);
```

---

# 27. JSX Expressions

Allowed:

```jsx
<h1>{10 + 20}</h1>
```

---

Output:

```text
30
```

---

Allowed:

```jsx
<h1>{user.name}</h1>
```

---

Allowed:

```jsx
<h1>{isLoggedIn ? "Yes" : "No"}</h1>
```

---

# 28. Common JSX Mistakes

---

Using:

```jsx
class=
```

instead of:

```jsx
className=
```

---

Using:

```jsx
for=
```

instead of:

```jsx
htmlFor=
```

---

Multiple root elements.

---

Missing closing tags.

---

Using:

```jsx
style="color:red"
```

instead of object syntax.

---

# 29. Interview Questions

---

### Q1. Why was React Fiber introduced?

```text
To support scheduling,
prioritization,
interruptible rendering,
and better performance.
```

---

### Q2. What is Fiber?

```text
A JavaScript object
representing a component.
```

---

### Q3. What are Fiber phases?

```text
Render Phase
Commit Phase
```

---

### Q4. Can Render Phase be interrupted?

```text
Yes
```

---

### Q5. Can Commit Phase be interrupted?

```text
No
```

---

### Q6. What is JSX?

```text
JavaScript XML
```

---

### Q7. Is JSX HTML?

```text
No
```

---

### Q8. What converts JSX?

```text
Babel
```

---

### Q9. Why use className?

```text
class is reserved
JavaScript keyword.
```

---

### Q10. Why use Fragments?

```text
Avoid unnecessary
DOM elements.
```

---

# Memory Tricks

### Fiber

```text
Small Units Of Work
```

---

### Scheduler

```text
Decides Priority
```

---

### Render Phase

```text
Calculate Changes
```

---

### Commit Phase

```text
Apply Changes
```

---

### JSX

```text
HTML-Like Syntax
Inside JavaScript
```

---

### Babel

```text
Converts JSX
Into JavaScript
```

---

# Quick Revision

```text
React Fiber
-----------

Introduced:
React 16

Purpose:
Better Rendering

Features:

✔ Scheduling
✔ Priorities
✔ Interruptible Rendering
✔ Concurrent Rendering

Fiber Phases
------------

Render Phase
      ↓
Calculate

Commit Phase
      ↓
Apply

JSX
---

JavaScript XML

Purpose:

Write UI Easily

Flow:

JSX
 ↓

Babel
 ↓

React.createElement()
 ↓

Virtual DOM

Important JSX Rules
-------------------

✔ One Parent Element
✔ Close Tags
✔ className
✔ htmlFor
✔ Curly Braces
✔ Camel Case Events
✔ Style As Object

Interview Keywords
------------------

Fiber
Scheduler
Render Phase
Commit Phase
JSX
React.createElement
Fragment
className
htmlFor
```

---

# Next Chapter

```text
Expressions in JSX
JSX vs HTML
Babel Transpiler
```

This chapter dives deeply into:

✔ Dynamic Rendering  
✔ Conditional Rendering  
✔ Loops in JSX  
✔ Babel Internals  
✔ JSX Compilation Pipeline  
✔ JSX Runtime  
✔ React.createElement Internals

---
# React Fundamentals - Chapter 5

> **Goal:** Understand Expressions in JSX, JSX vs HTML, Babel Transpiler, JSX Compilation Process, React.createElement(), Babel Internals, JSX Runtime, Dynamic Rendering, Conditional Rendering, Lists Rendering, and Interview Questions.

---

# 1. Expressions in JSX

Before understanding expressions, let's understand:

```text
What can be written inside JSX?
```

---

JSX allows:

```text
HTML-Like Markup
+
JavaScript Expressions
```

---

Example:

```jsx
const name = "John";

function App() {

 return (

  <h1>
   Hello {name}
  </h1>

 );
}
```

---

Output:

```text
Hello John
```

---

Notice:

```jsx
{name}
```

---

This is called:

```text
JSX Expression
```

---

# 2. What is an Expression?

Expression means:

```text
Something That Produces A Value
```

---

Examples:

```javascript
10 + 20
```

---

```javascript
user.name
```

---

```javascript
isLoggedIn ? "Yes" : "No"
```

---

All produce values.

---

# 3. Curly Braces in JSX

JavaScript expressions are written inside:

```jsx
{}
```

---

Example:

```jsx
<h1>{10 + 20}</h1>
```

---

Output:

```text
30
```

---

Example:

```jsx
<h1>{100 * 5}</h1>
```

---

Output:

```text
500
```

---

# 4. Variables Inside JSX

Example:

```jsx
const city = "Bangalore";

function App() {

 return (

  <h1>
   {city}
  </h1>

 );
}
```

---

Output:

```text
Bangalore
```

---

# 5. Object Properties Inside JSX

Example:

```jsx
const user = {

 name: "John",
 age: 25
};
```

---

JSX:

```jsx
<h1>
 {user.name}
</h1>
```

---

Output:

```text
John
```

---

# 6. Function Calls Inside JSX

Functions can be called.

---

Example:

```jsx
function getMessage() {

 return "Welcome";
}
```

---

JSX:

```jsx
<h1>
 {getMessage()}
</h1>
```

---

Output:

```text
Welcome
```

---

# 7. Arithmetic Expressions

Example:

```jsx
<h1>
 {50 + 25}
</h1>
```

---

Output:

```text
75
```

---

Example:

```jsx
<h1>
 {100 / 5}
</h1>
```

---

Output:

```text
20
```

---

# 8. Ternary Operator

Most common JSX expression.

---

Example:

```jsx
const isLoggedIn = true;
```

---

JSX:

```jsx
<h1>

 {
  isLoggedIn
   ? "Welcome"
   : "Please Login"
 }

</h1>
```

---

Output:

```text
Welcome
```

---

# 9. Conditional Rendering

Very important interview topic.

---

Example:

```jsx
function App() {

 const isAdmin = true;

 return (

  <div>

   {
    isAdmin
     ? <h1>Admin</h1>
     : <h1>User</h1>
   }

  </div>

 );
}
```

---

Output:

```text
Admin
```

---

# 10. Logical AND Rendering

Example:

```jsx
const isLoggedIn = true;
```

---

JSX:

```jsx
{
 isLoggedIn &&
 <h1>Welcome</h1>
}
```

---

Output:

```text
Welcome
```

---

If:

```jsx
false
```

---

Output:

```text
Nothing
```

---

# 11. Rendering Lists

One of the most used JSX concepts.

---

Example:

```jsx
const users = [

 "John",
 "David",
 "Smith"
];
```

---

JSX:

```jsx
<ul>

 {
  users.map(user =>

   <li>
    {user}
   </li>

  )
 }

</ul>
```

---

Output:

```html
<ul>
 <li>John</li>
 <li>David</li>
 <li>Smith</li>
</ul>
```

---

# 12. Why Keys Are Required?

Wrong:

```jsx
users.map(user =>

 <li>{user}</li>

)
```

---

React Warning:

```text
Each child should have unique key
```

---

Correct:

```jsx
users.map(user =>

 <li key={user}>
  {user}
 </li>

)
```

---

Reason:

```text
Helps React Identify Elements
```

during re-render.

---

# 13. Expressions Not Allowed

Allowed:

```jsx
{name}
```

---

Not Allowed:

```jsx
if(condition)
```

---

Wrong:

```jsx
{
 if(true)
 {
   return "Hello";
 }
}
```

---

Reason:

```text
if statement
is not an expression.
```

---

# 14. Statements vs Expressions

Important interview topic.

---

Expression:

```javascript
10 + 20
```

Produces value.

---

Statement:

```javascript
if(condition)
{
}
```

Performs action.

---

JSX accepts:

```text
Expressions Only
```

---

# 15. Using IIFE Inside JSX

Advanced concept.

---

Example:

```jsx
{
 (() => {

   return "Hello";

 })()
}
```

---

Output:

```text
Hello
```

---

Rarely used.

---

# 16. JSX vs HTML

One of the most asked interview questions.

---

Many developers think:

```text
JSX = HTML
```

---

Wrong.

---

JSX:

```text
JavaScript Syntax Extension
```

---

HTML:

```text
Markup Language
```

---

# 17. JSX vs HTML Comparison

| Feature | HTML | JSX |
|----------|----------|----------|
| Type | Markup Language | JavaScript Syntax |
| Browser Understands | Yes | No |
| Compilation Needed | No | Yes |
| Variables | No | Yes |
| Expressions | No | Yes |
| Dynamic Content | Limited | Easy |

---

# 18. Difference 1

HTML:

```html
<div class="container">
</div>
```

---

JSX:

```jsx
<div className="container">
</div>
```

---

Reason:

```text
class is JS keyword.
```

---

# 19. Difference 2

HTML:

```html
<label for="name">
```

---

JSX:

```jsx
<label htmlFor="name">
```

---

Reason:

```text
for is reserved word.
```

---

# 20. Difference 3

HTML:

```html
<input>
```

---

JSX:

```jsx
<input />
```

---

All tags must close.

---

# 21. Difference 4

HTML:

```html
<button onclick="">
```

---

JSX:

```jsx
<button onClick="">
```

---

Uses:

```text
Camel Case
```

---

# 22. Difference 5

HTML:

```html
<h1 style="color:red">
```

---

JSX:

```jsx
<h1

 style={{
  color: "red"
 }}

>
```

---

Style becomes:

```text
JavaScript Object
```

---

# 23. Difference 6

HTML Comments:

```html
<!-- Comment -->
```

---

JSX:

```jsx
{
 /* Comment */
}
```

---

# 24. Difference 7

HTML:

```html
<h1>Hello</h1>
```

---

JSX:

```jsx
<h1>{message}</h1>
```

---

Can directly use:

```text
Variables
Functions
Expressions
```

---

# 25. What is Babel?

One of the most important React topics.

---

Definition:

> Babel is a JavaScript compiler that converts modern JavaScript and JSX into browser-compatible JavaScript.

---

Simple Meaning:

```text
Translator
```

for JavaScript.

---

# 26. Why Babel Exists?

Browsers understand:

```javascript
ES5
```

well.

---

Modern JavaScript:

```javascript
Arrow Functions
Classes
Modules
JSX
```

may not work everywhere.

---

Babel converts:

```javascript
Modern JS
```

to:

```javascript
Older Compatible JS
```

---

# 27. JSX Compilation Process

Flow:

```text
JSX
 ↓

Babel
 ↓

React.createElement()
 ↓

Virtual DOM
 ↓

Real DOM
```

---

Most important React flow.

---

# 28. Example JSX Compilation

JSX:

```jsx
<h1>Hello</h1>
```

---

Babel converts:

```javascript
React.createElement(
 "h1",
 null,
 "Hello"
);
```

---

React converts:

```javascript
{
 type: "h1",
 props: {
  children: "Hello"
 }
}
```

---

Virtual DOM created.

---

# 29. Complex JSX Compilation

JSX:

```jsx
<div>

 <h1>Hello</h1>

 <p>React</p>

</div>
```

---

Converted:

```javascript
React.createElement(

 "div",

 null,

 React.createElement(
  "h1",
  null,
  "Hello"
 ),

 React.createElement(
  "p",
  null,
  "React"
 )

);
```

---

This is how nested Virtual DOM is built.

---

# 30. React.createElement()

Very important interview topic.

---

Definition:

```text
Creates Virtual DOM Objects
```

---

Syntax:

```javascript
React.createElement(

 type,
 props,
 children

);
```

---

Example:

```javascript
React.createElement(

 "h1",

 { className: "title" },

 "Hello"

);
```

---

Equivalent JSX:

```jsx
<h1 className="title">
 Hello
</h1>
```

---

# 31. JSX Runtime (React 17+)

Before React 17:

```jsx
import React
from "react";
```

Required.

---

Reason:

```text
JSX became React.createElement()
```

---

After React 17:

```jsx
No need to import React
```

for JSX.

---

New JSX Transform introduced.

---

Example:

```jsx
function App() {

 return <h1>Hello</h1>;
}
```

---

Works without:

```jsx
import React
```

---

# 32. Babel Plugins

Babel uses plugins.

---

Examples:

```text
Transform JSX
Transform Arrow Functions
Transform Classes
Transform Modules
```

---

Flow:

```text
Source Code
      ↓

Babel Plugins
      ↓

Transformed Code
```

---

# 33. Babel Presets

Common presets:

```text
@babel/preset-env

@babel/preset-react

@babel/preset-typescript
```

---

React projects commonly use:

```text
preset-react
```

---

# 34. Why Babel Is Important?

Without Babel:

```jsx
<h1>Hello</h1>
```

---

Browser sees:

```text
Invalid JavaScript
```

---

Babel converts it.

---

Now browser can execute code.

---

# 35. Complete Rendering Pipeline

One of the most important interview diagrams.

---

```text
JSX
 ↓

Babel
 ↓

React.createElement()
 ↓

Virtual DOM
 ↓

Fiber Tree
 ↓

Diffing
 ↓

Reconciliation
 ↓

Real DOM
 ↓

Browser Paint
```

---

# 36. Real World Example

Developer writes:

```jsx
<button>

 Click Me

</button>
```

---

Babel:

```javascript
React.createElement(
 "button",
 null,
 "Click Me"
);
```

---

React:

```text
Creates Virtual DOM
```

---

Fiber:

```text
Schedules Work
```

---

Diffing:

```text
Finds Changes
```

---

DOM Updates:

```text
Screen Updated
```

---

# 37. Interview Questions

---

### Q1. What are JSX expressions?

```text
JavaScript expressions
inside curly braces.
```

---

### Q2. Can we use if statements in JSX?

```text
No

JSX accepts expressions,
not statements.
```

---

### Q3. Difference between JSX and HTML?

```text
JSX is JavaScript syntax.
HTML is markup language.
```

---

### Q4. Why use className?

```text
class is reserved
JavaScript keyword.
```

---

### Q5. Why use htmlFor?

```text
for is reserved
JavaScript keyword.
```

---

### Q6. What is Babel?

```text
JavaScript Compiler
```

---

### Q7. Why is Babel required?

```text
Converts JSX
and modern JS
into browser-compatible JS.
```

---

### Q8. What does Babel convert JSX into?

```javascript
React.createElement()
```

---

### Q9. What does React.createElement return?

```text
Virtual DOM Object
```

---

### Q10. Explain React rendering pipeline.

```text
JSX
 ↓
Babel
 ↓
React.createElement()
 ↓
Virtual DOM
 ↓
Fiber
 ↓
Diffing
 ↓
Reconciliation
 ↓
Real DOM
```

---

# Memory Tricks

### JSX

```text
HTML-Like
JavaScript Syntax
```

---

### Curly Braces

```text
Run JavaScript
```

---

### Babel

```text
Translator
```

---

### React.createElement

```text
Create Virtual DOM
```

---

### Virtual DOM

```text
JavaScript Tree
```

---

### Real DOM

```text
Browser Tree
```

---

# Quick Revision

```text
Expressions In JSX
------------------

Use Curly Braces

Examples:

{name}

{10 + 20}

{isLoggedIn
 ? "Yes"
 : "No"}

JSX vs HTML
-----------

class
  ↓
className

for
  ↓
htmlFor

onclick
  ↓
onClick

style=""
  ↓
style={{}}

Babel
-----

Purpose:

Convert JSX
and Modern JS

Pipeline
--------

JSX
 ↓

Babel
 ↓

React.createElement()
 ↓

Virtual DOM
 ↓

Fiber
 ↓

Diffing
 ↓

Reconciliation
 ↓

Real DOM

Remember
----------

JSX
   ≠
HTML

Babel
   ↓
Compiler

React.createElement()
   ↓
Creates Virtual DOM Objects
```

---
