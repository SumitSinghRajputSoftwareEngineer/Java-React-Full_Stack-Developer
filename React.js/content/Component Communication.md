# Component Communication

> **Goal:** Master Component Communication in React — understand how components exchange data, Parent → Child communication, Child → Parent communication, Sibling communication, Context API, Global State Management, React Data Flow, State Lifting, Real-World Examples, Internal Working, Best Practices, and Interview Questions.

---

# 1. Why Component Communication is Needed?

React applications are built using:

```text
Components
```

---

Example:

```text
App
 ↓

Navbar

Sidebar

Dashboard

Footer
```

---

Each component is:

```text
Independent
```

---

But components often need to:

```text
Share Data

Trigger Actions

Update UI

Exchange Information
```

---

Example:

```text
Shopping Cart Count
```

---

Navbar needs it.

---

Cart page updates it.

---

Need communication.

---

# 2. React Data Flow

React follows:

```text
One-Way Data Flow
```

---

Also called:

```text
Unidirectional Data Flow
```

---

Visualization:

```text
Parent
  ↓
Child
  ↓
Grandchild
```

---

Data naturally flows:

```text
Top → Down
```

---

Not:

```text
Bottom → Up
```

---

This is a core React principle.

---

# Example

```jsx
function Parent() {

  const name = "Sumit";

  return (

    <Child
      name={name}
    />

  );

}
```

---

Data moves:

```text
Parent
 ↓

Child
```

---

# Component Communication Types

---

Most common communication patterns:

```text
1. Parent → Child

2. Child → Parent

3. Sibling → Sibling

4. Context API

5. Global State Management
```

---

# Visualization

```text
Parent
 ↓

Child

----------------

Child
 ↑

Parent

----------------

Sibling ↔ Sibling

----------------

Context API

----------------

Global State
```

---

# Parent to Child Communication

---

# 3. What is Parent → Child Communication?

Most common communication type.

---

Parent sends data using:

```text
Props
```

---

Visualization:

```text
Parent
  ↓ Props
Child
```

---

# Example

Parent:

```jsx
function Parent() {

  return (

    <Child
      name="Sumit"
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

Output:

```text
Sumit
```

---

Data flowed:

```text
Parent → Child
```

---

# 4. Multiple Props

Parent:

```jsx
<Child

 name="Sumit"

 age={25}

 city="Bangalore"

/>
```

---

Child:

```jsx
function Child({

 name,

 age,

 city

}) {

}
```

---

Received:

```text
3 Values
```

---

# 5. Passing Objects

Parent:

```jsx
const user = {

  id: 1,

  name: "John"

};
```

---

```jsx
<Child
 user={user}
/>
```

---

Child:

```jsx
function Child({

 user

}) {

}
```

---

Access:

```jsx
user.name
```

---

# 6. Passing Functions

Functions can also be props.

---

Parent:

```jsx
function Parent() {

  function greet() {

    alert("Hello");

  }

  return (

    <Child
      greet={greet}
    />

  );

}
```

---

Child:

```jsx
<button
 onClick={greet}
>
 Click
</button>
```

---

Child can execute:

```text
Parent Function
```

---

Very important.

---

# Child to Parent Communication

---

# 7. Why Direct Child → Parent Is Not Possible?

React data flow:

```text
Parent
 ↓
Child
```

---

Not:

```text
Child
 ↑
Parent
```

---

Therefore:

```text
Child Cannot Directly Modify Parent
```

---

Need workaround.

---

# Solution

Parent passes:

```text
Callback Function
```

---

Child calls it.

---

# Visualization

```text
Parent
 ↓ Callback

Child

Child Calls Callback
 ↑

Parent Updates State
```

---

# Example

Parent:

```jsx
function Parent() {

  const [message,
  setMessage]
  =
  useState("");

  function receiveData(data) {

    setMessage(data);

  }

  return (

    <Child
      sendData={
        receiveData
      }
    />

  );

}
```

---

Child:

```jsx
function Child({

 sendData

}) {

  return (

    <button

      onClick={() =>

        sendData(
         "Hello Parent"
        )

      }

    >

      Send

    </button>

  );

}
```

---

Output:

```text
Hello Parent
```

---

Data effectively moved:

```text
Child → Parent
```

---

Using:

```text
Callback Function
```

---

# Real-World Example

---

# 8. Search Component

Parent:

```jsx
<Search />
```

---

Child:

```jsx
<input />
```

---

User types:

```text
Laptop
```

---

Child sends value:

```text
Laptop
```

---

To parent.

---

Parent performs:

```text
API Call
```

---

Common pattern.

---

# Sibling Communication

---

# 9. What is Sibling Communication?

Example:

```text
Parent
 ↓

Child A

Child B
```

---

Child A wants to send data:

```text
To Child B
```

---

Cannot directly communicate.

---

Need:

```text
Common Parent
```

---

# Visualization

```text
Child A
   ↑
 Parent
   ↓
Child B
```

---

# Solution

Use:

```text
Lifting State Up
```

---

# Example

Parent:

```jsx
function Parent() {

  const [message,
  setMessage]
  =
  useState("");

  return (

    <>

      <ChildA
        setMessage={
          setMessage
        }
      />

      <ChildB
        message={
          message
        }
      />

    </>

  );

}
```

---

Child A:

```jsx
<button

 onClick={() =>

  setMessage(
   "Hello"
  )

 }

>
 Send
</button>
```

---

Child B:

```jsx
<h1>
 {message}
</h1>
```

---

Flow:

```text
Child A
   ↓

Parent State

   ↓

Child B
```

---

# 10. Why Lifting State Up?

State belongs to:

```text
Closest Common Parent
```

---

Benefits:

```text
Single Source Of Truth
```

---

Avoids:

```text
Duplicated State
```

---

# Context API

---

# 11. What Problem Does Context Solve?

Imagine:

```text
App
 ↓

Navbar
 ↓

Menu
 ↓

Profile
```

---

Need:

```text
Current User
```

---

Without Context:

```text
Pass Through Every Component
```

---

Called:

```text
Prop Drilling
```

---

# Prop Drilling Visualization

```text
App
 ↓ user

Navbar
 ↓ user

Menu
 ↓ user

Profile
```

---

Annoying.

---

# Context API Solution

```text
App

 ↓

Context

 ↓

Profile
```

---

Direct access.

---

# 12. Creating Context

```jsx
import {

 createContext

}
from "react";
```

---

```jsx
const UserContext =

createContext();
```

---

Context created.

---

# 13. Provider

```jsx
<UserContext.Provider

 value={"Sumit"}

>

 <App />

</UserContext.Provider>
```

---

Provides data.

---

# 14. Consumer Using useContext

```jsx
const user =

useContext(
 UserContext
);
```

---

Output:

```text
Sumit
```

---

No prop drilling.

---

# Context Flow

```text
Provider
   ↓

Context

   ↓

Any Component
```

---

# Real-World Context Examples

---

# 15. Theme Context

Store:

```text
Dark Mode

Light Mode
```

---

Available globally.

---

# 16. Auth Context

Store:

```text
User

Role

Token
```

---

Used throughout app.

---

# 17. Language Context

Store:

```text
English

Hindi

Kannada
```

---

Global access.

---

# Context Limitations

---

# 18. Context Is Not Redux

Many beginners think:

```text
Context = Redux
```

---

Not exactly.

---

Context provides:

```text
Data Sharing
```

---

Not advanced state management.

---

# Problem

Large context updates:

```text
Can Re-render
Many Components
```

---

Performance issue.

---

# Global State Management

---

# 19. What is Global State?

State needed by:

```text
Many Components
```

---

Examples:

```text
Logged User

Theme

Shopping Cart

Language

Notifications
```

---

Should not live inside:

```text
Single Component
```

---

Needs:

```text
Central Storage
```

---

# Visualization

```text
Global Store

 ↓

Navbar

Cart

Dashboard

Profile
```

---

Everyone accesses same state.

---

# 20. Why Global State?

Without it:

```text
Prop Drilling

Complex Communication

Duplicate State
```

---

Problems increase as app grows.

---

# Popular Global State Solutions

---

# 21. Context API

Built into React.

---

Good for:

```text
Theme

Auth

Language
```

---

Small-to-medium apps.

---

# 22. Redux

Most popular.

---

Provides:

```text
Central Store
```

---

Visualization:

```text
Redux Store

 ↓

All Components
```

---

Benefits:

```text
Predictable

Scalable

Powerful
```

---

# 23. Zustand

Modern state library.

---

Very simple.

---

Example:

```jsx
const useStore =
create((set) => ({

 count: 0

}));
```

---

Popular today.

---

# 24. Recoil

Created by:

```text
Meta (Facebook)
```

---

Atom-based state management.

---

Useful for:

```text
Complex React Apps
```

---

# Communication Comparison

| Communication Type | Best For |
|------------|------------|
| Parent → Child | Props |
| Child → Parent | Callback Functions |
| Sibling → Sibling | Lift State Up |
| Deep Component Sharing | Context API |
| Large Application State | Redux/Zustand/Recoil |

---

# Real-World Architecture

---

# 25. E-Commerce Example

Global State:

```text
User

Cart

Wishlist
```

---

Context:

```text
Theme

Language
```

---

Props:

```text
Product Data
```

---

Callback:

```text
Add To Cart
```

---

Used together.

---

# Common Mistakes

---

# 26. Too Much Prop Drilling

Wrong:

```text
App
 ↓
A
 ↓
B
 ↓
C
 ↓
D
```

---

Use:

```text
Context
```

---

# 27. Using Context For Everything

Wrong.

---

Every update may trigger:

```text
Re-renders
```

---

Use Context wisely.

---

# 28. Duplicate State

Wrong:

```jsx
Parent State

Child State
```

---

For same data.

---

Leads to:

```text
Inconsistency
```

---

# Best Practices

---

# 29. Use Props For Simple Communication

Example:

```text
Parent → Child
```

---

Most efficient.

---

# 30. Lift State Up For Siblings

Example:

```text
Child A ↔ Child B
```

---

Through parent.

---

# 31. Use Context For Shared Data

Examples:

```text
Theme

Auth

Language
```

---

# 32. Use Redux/Zustand For Large Apps

When:

```text
Many Components

Complex State

Frequent Updates
```

---

# Interview Questions

---

### Q1. What is React's data flow?

```text
Unidirectional
(Top → Down)
```

---

### Q2. How does Parent → Child communication happen?

```text
Props
```

---

### Q3. How does Child → Parent communication happen?

```text
Callback Functions
```

---

### Q4. How do siblings communicate?

```text
Lift State Up
```

---

### Q5. What problem does Context API solve?

```text
Prop Drilling
```

---

### Q6. What is Prop Drilling?

```text
Passing Props Through
Multiple Components
```

---

### Q7. What is Global State?

```text
State Shared
Across Application
```

---

### Q8. When should Context API be used?

```text
Theme

Auth

Language
```

---

### Q9. What are popular state management libraries?

```text
Redux

Zustand

Recoil
```

---

### Q10. Context API vs Redux?

Context:

```text
Data Sharing
```

---

Redux:

```text
Full State Management
```

---

# Memory Tricks

### Parent → Child

```text
Props
```

---

### Child → Parent

```text
Callback Function
```

---

### Sibling → Sibling

```text
Lift State Up
```

---

### Deep Components

```text
Context API
```

---

### Large Applications

```text
Redux / Zustand
```

---

### React Data Flow

```text
Top → Down
```

---

# Complete Communication Flow

```text
Parent → Child
 ↓
Props

Child → Parent
 ↓
Callback Functions

Sibling ↔ Sibling
 ↓
Lift State Up

Deep Components
 ↓
Context API

Application Wide State
 ↓
Redux / Zustand / Recoil
```

---

# Quick Revision

```text
Component Communication
-----------------------

React Data Flow:
Top → Down

Communication Types:

1. Parent → Child
   ↓
   Props

2. Child → Parent
   ↓
   Callback Functions

3. Sibling Communication
   ↓
   Lift State Up

4. Deep Component Sharing
   ↓
   Context API

5. Global State
   ↓
   Redux / Zustand / Recoil

Context Solves:
✔ Prop Drilling

Global State Solves:
✔ Application Wide State

Remember:

Props
 ↓
Parent → Child

Callback
 ↓
Child → Parent

State Lifting
 ↓
Sibling Communication

Context
 ↓
Shared Data

Redux
 ↓
Global State
```

---