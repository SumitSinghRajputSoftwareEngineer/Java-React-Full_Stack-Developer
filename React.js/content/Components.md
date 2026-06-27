 # Components

> **Goal:** Understand Functional Components, Class Components, Component Naming Conventions, Reusable Components, Smart vs Dumb Components, Container vs Presentational Components, Pure Components, Higher Order Components (HOC), internal working, real-world examples, best practices, and interview questions.

---

# 1. What is a Component?

A Component is the fundamental building block of React.

---

Think of a React application as:

```text
LEGO Blocks
```

---

Each block represents:

```text
A Component
```

---

Example:

```text
Amazon Website
```

can be divided into:

```text
Navbar

Search Bar

Product Card

Cart

Footer
```

---

Each section becomes:

```text
Independent Component
```

---

Visualization:

```text
App
│
├── Navbar
│
├── SearchBar
│
├── ProductList
│     │
│     ├── ProductCard
│     ├── ProductCard
│     └── ProductCard
│
└── Footer
```

---

Benefits:

```text
Reusable
Maintainable
Scalable
Testable
```

---

# 2. Types of Components

React mainly has:

```text
1. Functional Components

2. Class Components
```

---

Modern React:

```text
Mostly Functional Components
```

---

Class Components:

```text
Legacy
Still Asked In Interviews
```

---

# Functional Components

---

# 3. What is a Functional Component?

A Functional Component is a JavaScript function that returns JSX.

---

Example:

```jsx
function Welcome() {

 return <h1>Hello React</h1>;
}
```

---

Usage:

```jsx
<Welcome />
```

---

Output:

```text
Hello React
```

---

# 4. Functional Component Syntax

---

## Traditional Function

```jsx
function Header() {

 return <h1>Header</h1>;
}
```

---

## Arrow Function

```jsx
const Header = () => {

 return <h1>Header</h1>;
};
```

---

Most modern projects use:

```text
Arrow Functions
```

---

# 5. Functional Component Example

```jsx
function User() {

 return (

  <div>

   <h2>John</h2>

   <p>Developer</p>

  </div>

 );
}
```

---

Usage:

```jsx
<User />
```

---

Output:

```html
<div>
 <h2>John</h2>
 <p>Developer</p>
</div>
```

---

# 6. Functional Components with Props

Example:

```jsx
function User(props) {

 return (

  <h1>
   {props.name}
  </h1>

 );
}
```

---

Usage:

```jsx
<User name="John" />
```

---

Output:

```text
John
```

---

# 7. Destructuring Props

Recommended approach.

---

Instead of:

```jsx
function User(props) {

 return <h1>{props.name}</h1>;
}
```

---

Use:

```jsx
function User({ name }) {

 return <h1>{name}</h1>;
}
```

---

Cleaner code.

---

# 8. Why Functional Components Became Popular?

Before React Hooks:

```text
Class Components
```

handled state.

---

After Hooks:

```text
Functional Components
```

can handle:

```text
State
Effects
Context
Refs
```

---

Result:

```text
Class Components Rarely Needed
```

---

# 9. Advantages of Functional Components

---

## Simpler Syntax

Less code.

---

## Better Readability

Easy to understand.

---

## Hooks Support

```jsx
useState()

useEffect()

useContext()
```

---

## Better Performance Optimization

Works well with:

```jsx
React.memo()
```

---

# Class Components

---

# 10. What is a Class Component?

Class Component is a JavaScript class that extends:

```jsx
React.Component
```

---

Example:

```jsx
import React from "react";

class Welcome
extends React.Component {

 render() {

  return (
   <h1>Hello React</h1>
  );
 }
}
```

---

Usage:

```jsx
<Welcome />
```

---

# 11. Internal Structure

```jsx
class User
extends React.Component {

 render() {

  return (

   <h1>User</h1>

  );
 }
}
```

---

Important:

```text
render()
```

must return JSX.

---

# 12. Class Component with Props

```jsx
class User
extends React.Component {

 render() {

  return (

   <h1>

    {this.props.name}

   </h1>

  );
 }
}
```

---

Usage:

```jsx
<User name="John" />
```

---

Output:

```text
John
```

---

# 13. Class Component State

```jsx
class Counter
extends React.Component {

 state = {

  count: 0
 };

 render() {

  return (

   <h1>

    {this.state.count}

   </h1>

  );
 }
}
```

---

Access using:

```jsx
this.state
```

---

# 14. Updating State

```jsx
this.setState({

 count: 1
});
```

---

Never do:

```jsx
this.state.count = 1;
```

---

Wrong approach.

---

# 15. Functional vs Class Components

| Feature | Functional | Class |
|-----------|-----------|-----------|
| Syntax | Simple | Verbose |
| Hooks | Yes | No |
| State | useState | this.state |
| Lifecycle | useEffect | Lifecycle Methods |
| Modern Usage | Yes | Rare |
| Learning Curve | Easy | Hard |

---

Interview Answer:

```text
Use Functional Components
for all new development.
```

---

# Component Naming Conventions

---

# 16. Why Naming Matters?

Bad naming:

```jsx
function x() {}
```

---

Good naming:

```jsx
function UserCard() {}
```

---

Code becomes readable.

---

# 17. PascalCase Rule

Component names must start with:

```text
Capital Letter
```

---

Correct:

```jsx
function UserCard() {}
```

---

Wrong:

```jsx
function userCard() {}
```

---

Reason:

React treats lowercase names as:

```html
HTML Elements
```

---

# 18. File Naming Convention

Recommended:

```text
UserCard.jsx

Navbar.jsx

ProductCard.jsx
```

---

Avoid:

```text
usercard.jsx

navbar.jsx
```

---

# 19. Folder Naming Convention

Example:

```text
components/
│
├── UserCard/
│    ├── UserCard.jsx
│    └── UserCard.css
```

---

Common in enterprise projects.

---

# Reusable Components

---

# 20. What is a Reusable Component?

Reusable Component:

```text
Write Once
Use Multiple Times
```

---

Example:

Button.

---

Without reuse:

```jsx
<button>Save</button>

<button>Delete</button>

<button>Update</button>
```

---

Repeated code.

---

# 21. Reusable Button Component

```jsx
function Button({

 label

}) {

 return (

  <button>

   {label}

  </button>

 );
}
```

---

Usage:

```jsx
<Button label="Save" />

<Button label="Delete" />

<Button label="Update" />
```

---

Same component reused.

---

# 22. Real World Example

Amazon Product Card:

```text
Same Layout
Different Data
```

---

Component:

```jsx
<ProductCard />
```

---

Used hundreds of times.

---

# Smart vs Dumb Components

---

# 23. Smart Components

Also called:

```text
Container Components
```

---

Responsibilities:

```text
Business Logic
API Calls
State Management
Data Processing
```

---

Example:

```jsx
function UsersContainer() {

 const [users, setUsers]
 = useState([]);

 return (
  <UsersList
   users={users}
  />
 );
}
```

---

Handles:

```text
Logic
```

---

# 24. Dumb Components

Also called:

```text
Presentational Components
```

---

Responsibilities:

```text
Display UI
```

---

Example:

```jsx
function UsersList({

 users

}) {

 return (

  <ul>

   {
    users.map(user =>

      <li>
       {user.name}
      </li>

    )
   }

  </ul>

 );
}
```

---

Handles:

```text
Presentation Only
```

---

# 25. Smart vs Dumb Comparison

| Feature | Smart | Dumb |
|----------|----------|----------|
| State | Yes | Usually No |
| API Calls | Yes | No |
| Business Logic | Yes | No |
| UI Rendering | Yes | Yes |
| Reusability | Lower | Higher |

---

Memory Trick:

```text
Smart
 ↓
Think

Dumb
 ↓
Display
```

---

# Container vs Presentational Components

---

# 26. Container Components

Responsible for:

```text
Fetching Data
Managing State
```

---

Example:

```jsx
<UserContainer />
```

---

# 27. Presentational Components

Responsible for:

```text
Displaying Data
```

---

Example:

```jsx
<UserList />
```

---

Visualization:

```text
Container
      ↓

Fetch Data
      ↓

Pass Props
      ↓

Presentational
      ↓

Display UI
```

---

# 28. Real Example

Container:

```jsx
function ProductContainer() {

 const products = fetchProducts();

 return (

  <ProductList
   products={products}
  />

 );
}
```

---

Presentational:

```jsx
function ProductList({

 products

}) {

 return (

  <>
   {
    products.map(...)
   }
  </>

 );
}
```

---

# Pure Components

---

# 29. What is a Pure Component?

A Pure Component prevents unnecessary re-renders.

---

Idea:

```text
Same Input
      ↓
Same Output
```

---

No need to re-render.

---

# 30. Problem Without Optimization

Parent re-renders:

```text
Child Also Re-renders
```

---

Even when child data unchanged.

---

Performance wasted.

---

# 31. React.PureComponent

Class Component optimization.

---

Example:

```jsx
class User
extends React.PureComponent {

 render() {

  return <h1>User</h1>;
 }
}
```

---

React performs:

```text
Shallow Comparison
```

---

Before re-rendering.

---

# 32. React.memo()

Functional Component equivalent.

---

Example:

```jsx
const User = React.memo(

 function User() {

  return <h1>User</h1>;
 }

);
```

---

Now React skips re-render if:

```text
Props Unchanged
```

---

# 33. Shallow Comparison

Example:

Old:

```javascript
{
 name: "John"
}
```

---

New:

```javascript
{
 name: "John"
}
```

---

React sees:

```text
No Change
```

---

Skip render.

---

# 34. Benefits of Pure Components

```text
Better Performance

Less Rendering

Optimized UI
```

---

# Higher Order Components (HOC)

---

# 35. What is HOC?

One of the most important React interview topics.

---

Definition:

> A Higher Order Component is a function that takes a component and returns a new enhanced component.

---

Simple Meaning:

```text
Component Wrapper
```

---

# 36. HOC Syntax

```javascript
HOC(Component)
```

---

Returns:

```javascript
EnhancedComponent
```

---

Visualization:

```text
Component
     ↓

HOC
     ↓

Enhanced Component
```

---

# 37. Real Life Example

Mobile Cover.

---

Phone:

```text
Component
```

---

Cover:

```text
HOC
```

---

Result:

```text
Enhanced Phone
```

---

# 38. HOC Example

Original Component:

```jsx
function User() {

 return <h1>User</h1>;
}
```

---

HOC:

```jsx
function withLogger(

 Component

) {

 return function() {

  console.log("Rendered");

  return <Component />;
 };
}
```

---

Enhanced Component:

```jsx
const EnhancedUser =

withLogger(User);
```

---

Usage:

```jsx
<EnhancedUser />
```

---

Output:

```text
Rendered

User
```

---

# 39. Authentication HOC Example

```jsx
function withAuth(

 Component

) {

 return function(props) {

  const isLoggedIn = true;

  return isLoggedIn

   ? <Component {...props} />

   : <h1>Login First</h1>;
 };
}
```

---

Usage:

```jsx
const ProtectedDashboard =

withAuth(Dashboard);
```

---

# 40. HOC Flow

```text
Original Component
         ↓

HOC
         ↓

Adds Logic
         ↓

Returns Enhanced Component
```

---

# 41. HOC vs Component

Component:

```text
Displays UI
```

---

HOC:

```text
Enhances Component
```

---

# 42. Modern Alternative

Many HOC use cases today are replaced by:

```text
Custom Hooks
```

---

Example:

Instead of:

```jsx
withAuth()
```

---

Use:

```jsx
useAuth()
```

---

Cleaner approach.

---

# Interview Questions

---

### Q1. What is a Component?

```text
Reusable building block
of React UI.
```

---

### Q2. Types of Components?

```text
Functional

Class
```

---

### Q3. Which component type is preferred today?

```text
Functional Components
```

---

### Q4. Why Functional Components are popular?

```text
Hooks support.

Less code.

Better readability.
```

---

### Q5. What naming convention should components follow?

```text
PascalCase
```

---

### Q6. What is a reusable component?

```text
Write Once
Use Multiple Times
```

---

### Q7. Smart vs Dumb Component?

Smart:

```text
Logic
```

---

Dumb:

```text
UI
```

---

### Q8. What is PureComponent?

```text
Optimized component
using shallow comparison.
```

---

### Q9. Functional equivalent of PureComponent?

```jsx
React.memo()
```

---

### Q10. What is HOC?

```text
Function that accepts
a component and returns
an enhanced component.
```

---

### Q11. HOC Full Form?

```text
Higher Order Component
```

---

### Q12. Give real-world HOC examples.

```text
Authentication

Logging

Authorization

Analytics Tracking
```

---

# Memory Tricks

### Component

```text
Reusable UI Block
```

---

### Functional Component

```text
Function → JSX
```

---

### Class Component

```text
Class → render()
```

---

### Smart Component

```text
Think
```

---

### Dumb Component

```text
Display
```

---

### Pure Component

```text
Skip Unnecessary Renders
```

---

### HOC

```text
Wrap And Enhance
```

---

# Quick Revision

```text
Components
----------

Reusable UI Building Blocks

Types
-----

1. Functional Components

2. Class Components

Naming Convention
-----------------

✔ PascalCase

Examples:

UserCard

Navbar

ProductList

Reusable Components
-------------------

Write Once
Use Many Times

Examples:

Button
Input
Card

Smart Components
----------------

✔ State
✔ API Calls
✔ Logic

Dumb Components
---------------

✔ UI Only

Container Components
--------------------

Fetch Data
Manage State

Presentational Components
-------------------------

Display Data

Pure Components
---------------

Prevent Unnecessary Renders

Class:

React.PureComponent

Functional:

React.memo()

Higher Order Components
-----------------------

Function
     ↓

Accept Component
     ↓

Return Enhanced Component

Examples:

withAuth()

withLogger()

withAnalytics()

Remember
---------

Functional Component
      ↓
Preferred

Class Component
      ↓
Legacy

Smart
      ↓
Logic

Dumb
      ↓
UI

Pure Component
      ↓
Optimization

HOC
      ↓
Wrap + Enhance
```

---

**Next Topic:** Props (Props Basics, Passing Props, Props Destructuring, Default Props, Children Props, PropTypes, Props Drilling, and Composition Patterns).