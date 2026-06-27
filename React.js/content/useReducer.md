# useReducer Hook

> **Goal:** Understand useReducer from scratch, why React created it, how it works internally, Reducers, Actions, Dispatch, State Transitions, useReducer vs useState, Complex State Management, Context + useReducer Pattern, Internal React Flow, Real-World Examples, Best Practices, Common Mistakes, Interview Questions, and React Internals.

---

# 1. What Problem Does useReducer Solve?

Suppose we have a simple counter.

```jsx
const [count, setCount] = useState(0);
```

---

Increment:

```jsx
setCount(count + 1);
```

---

Works perfectly.

---

But imagine a larger application.

State contains:

```text
User

Products

Cart

Orders

Filters

Loading Status

Errors
```

---

Using many useState hooks:

```jsx
const [user, setUser] = useState();
const [cart, setCart] = useState();
const [products, setProducts] = useState();
const [loading, setLoading] = useState();
const [error, setError] = useState();
```

---

Application becomes:

```text
Hard To Manage

Hard To Debug

Hard To Scale
```

---

Need:

```text
Centralized State Updates
```

---

React introduced:

```text
useReducer()
```

---

# 2. What is useReducer?

Definition:

> useReducer is a React Hook used to manage complex state logic using a reducer function.

---

Simple Meaning:

```text
State Updates
Through Rules
```

---

Instead of:

```jsx
setState(...)
```

---

We do:

```jsx
dispatch(action)
```

---

Reducer decides:

```text
How State Changes
```

---

# 3. Real Life Example

Imagine:

```text
Bank Account
```

---

Operations:

```text
Deposit

Withdraw

Reset
```

---

Instead of updating balance everywhere:

```text
Central Department
Handles Rules
```

---

That department is:

```text
Reducer
```

---

# 4. Core Concepts

useReducer has 4 major parts:

```text
State

Action

Reducer

Dispatch
```

---

Understanding these is mandatory.

---

# 5. State

State stores data.

Example:

```jsx
{
 count: 0
}
```

---

Current application data.

---

# 6. Action

Action describes:

```text
What Happened
```

---

Examples:

```text
INCREMENT

DECREMENT

RESET
```

---

Action does NOT update state.

---

It only describes an event.

---

# 7. Dispatch

Dispatch sends action to reducer.

---

Example:

```jsx
dispatch({
 type: "INCREMENT"
});
```

---

Think:

```text
Send Request
```

---

# 8. Reducer

Reducer contains update rules.

---

Example:

```jsx
function reducer(
 state,
 action
) {

 switch(action.type) {

  case "INCREMENT":

   return {
    count: state.count + 1
   };

  default:

   return state;
 }
}
```

---

Reducer decides:

```text
Current State
       +
Action
       ↓
New State
```

---

# 9. Visual Flow

```text
dispatch(action)
        ↓

Reducer
        ↓

New State
        ↓

Re-render
```

---

Most important useReducer diagram.

---

# 10. Syntax

```jsx
const [

 state,

 dispatch

]
=
useReducer(

 reducer,

 initialState

);
```

---

Parameters:

```jsx
useReducer(

 reducerFunction,

 initialState

);
```

---

Returns:

```jsx
[
 state,

 dispatch
]
```

---

# 11. First Example

## Reducer

```jsx
function reducer(
 state,
 action
) {

 switch(action.type) {

  case "INCREMENT":

   return {

    count:
    state.count + 1

   };

  default:

   return state;
 }
}
```

---

## Component

```jsx
import {
 useReducer
}
from "react";
```

---

```jsx
const initialState = {

 count: 0

};
```

---

```jsx
function App() {

 const [

  state,

  dispatch

 ] = useReducer(

  reducer,

  initialState

 );

 return (

  <>
   <h1>

    {state.count}

   </h1>

   <button

    onClick={() =>

     dispatch({

      type:
      "INCREMENT"

     })

    }

   >

    Increment

   </button>

  </>

 );
}
```

---

Output:

```text
0
```

---

Click:

```text
Increment
```

---

Output:

```text
1
```

---

# 12. Internal Working

User clicks button.

---

Dispatch executes.

```jsx
dispatch({
 type: "INCREMENT"
});
```

---

React calls reducer.

```jsx
reducer(
 state,
 action
);
```

---

Reducer returns:

```jsx
{
 count: 1
}
```

---

React stores:

```text
New State
```

---

Component re-renders.

---

Flow:

```text
Click
 ↓

Dispatch
 ↓

Reducer
 ↓

New State
 ↓

Re-render
```

---

# 13. Why Called Reducer?

Term comes from:

```text
Functional Programming
```

---

Array reduce:

```jsx
array.reduce(...)
```

---

Takes:

```text
Current Value

+

Operation

↓

New Value
```

---

Reducer follows same idea.

---

# 14. Reducer Formula

```text
Current State
       +
Action
       ↓
New State
```

---

Example:

```text
Count = 5

Action = Increment

Result = 6
```

---

# Multiple Actions

---

# 15. Add Increment & Decrement

```jsx
function reducer(
 state,
 action
) {

 switch(action.type) {

  case "INCREMENT":

   return {

    count:
    state.count + 1

   };

  case "DECREMENT":

   return {

    count:
    state.count - 1

   };

  default:

   return state;
 }
}
```

---

Dispatch:

```jsx
dispatch({
 type: "DECREMENT"
});
```

---

Output:

```text
Count Reduced
```

---

# 16. Reset Action

```jsx
case "RESET":

 return {
  count: 0
 };
```

---

Dispatch:

```jsx
dispatch({
 type: "RESET"
});
```

---

State resets.

---

# Action Payload

---

# 17. What is Payload?

Additional data sent with action.

---

Example:

```jsx
dispatch({

 type: "INCREMENT",

 payload: 5

});
```

---

Payload:

```text
5
```

---

# 18. Reducer With Payload

```jsx
case "INCREMENT":

 return {

  count:

  state.count +

  action.payload

 };
```

---

Current:

```text
10
```

---

Payload:

```text
5
```

---

Result:

```text
15
```

---

# 19. Why Payload?

Allows dynamic updates.

---

Examples:

```text
Add Product

Update User

Add Quantity

Set Theme
```

---

# Complex State Example

---

# 20. Managing User State

Initial State:

```jsx
const initialState = {

 name: "",

 email: "",

 loggedIn: false

};
```

---

Reducer:

```jsx
function reducer(
 state,
 action
) {

 switch(action.type) {

  case "LOGIN":

   return {

    ...state,

    loggedIn: true,

    name:
    action.payload.name,

    email:
    action.payload.email

   };

  case "LOGOUT":

   return {

    ...state,

    loggedIn: false

   };

  default:

   return state;
 }
}
```

---

Dispatch:

```jsx
dispatch({

 type: "LOGIN",

 payload: {

  name: "John",

  email:
  "john@gmail.com"

 }

});
```

---

State updates.

---

# Why useReducer Over useState?

---

# 21. useState Example

```jsx
setUser(...)

setLoading(...)

setError(...)

setCart(...)
```

---

Many update functions.

---

Code becomes messy.

---

# 22. useReducer Example

```jsx
dispatch({
 type: "LOGIN"
});

dispatch({
 type: "LOGOUT"
});

dispatch({
 type: "ADD_PRODUCT"
});
```

---

Centralized logic.

---

Cleaner.

---

# 23. Comparison

| Feature | useState | useReducer |
|----------|----------|----------|
| Simplicity | High | Medium |
| Complex Logic | Poor | Excellent |
| Large State | Difficult | Easy |
| Multiple Updates | Hard | Easy |
| Scalability | Medium | High |

---

# When To Use useReducer?

---

# 24. Use useState When

State is simple.

Examples:

```text
Counter

Input

Toggle

Theme Switch
```

---

# 25. Use useReducer When

State is complex.

Examples:

```text
Authentication

Shopping Cart

Multi-Step Forms

Dashboard State

Large Applications
```

---

# Shopping Cart Example

---

# 26. Initial State

```jsx
const initialState = {

 cart: []

};
```

---

# 27. Add Product

Action:

```jsx
dispatch({

 type: "ADD_PRODUCT",

 payload: product

});
```

---

Reducer:

```jsx
case "ADD_PRODUCT":

 return {

  ...state,

  cart: [

   ...state.cart,

   action.payload

  ]

 };
```

---

# 28. Remove Product

```jsx
case "REMOVE_PRODUCT":

 return {

  ...state,

  cart:

  state.cart.filter(

   item =>

   item.id !==

   action.payload

  )

 };
```

---

Very common production use case.

---

# useReducer + useContext

---

# 29. Most Popular Pattern

Used heavily in React applications.

---

Flow:

```text
useReducer
      ↓

Context
      ↓

Global State
```

---

# 30. Why?

Reducer:

```text
Stores Logic
```

---

Context:

```text
Shares Logic
```

---

Together:

```text
Mini Redux
```

---

# 31. Architecture

```text
Reducer
    ↓

Provider
    ↓

Components
```

---

Any component:

```jsx
dispatch(...)
```

---

Updates global state.

---

# Lazy Initialization

---

# 32. Third Parameter

Syntax:

```jsx
useReducer(

 reducer,

 initialArg,

 initFunction

);
```

---

Rare but important.

---

# 33. Example

```jsx
function init(value) {

 return {

  count: value

 };
}
```

---

```jsx
useReducer(

 reducer,

 10,

 init

);
```

---

Initial state:

```text
Computed Once
```

---

Optimization.

---

# Internal React Working

---

# 34. Fiber Storage

Just like:

```text
useState
```

---

Reducer state stored inside:

```text
Fiber Node
```

---

Visualization:

```text
Fiber
 ↓

Hooks
 ↓

Reducer State
```

---

# 35. Dispatch Internals

When dispatch executes:

```jsx
dispatch(action);
```

---

React creates:

```text
Update Object
```

---

Places it into:

```text
Update Queue
```

---

# 36. Update Queue

Visualization:

```text
Action1
   ↓

Action2
   ↓

Action3
```

---

Reducer processes queue.

---

Generates final state.

---

# 37. Why Dispatch Stable?

Important interview question.

---

Dispatch function:

```text
Never Changes
```

---

Across renders.

---

Meaning:

```jsx
dispatch === dispatch
```

---

Always true.

---

Useful in dependencies.

---

# Common Mistakes

---

# 38. Mutating State

Wrong:

```jsx
state.count++;
```

---

Bad.

---

Must return new state.

---

Correct:

```jsx
return {

 count:
 state.count + 1

};
```

---

# 39. Forgetting Default Case

Wrong:

```jsx
switch(action.type) {

}
```

---

Correct:

```jsx
default:

 return state;
```

---

Always required.

---

# 40. Side Effects Inside Reducer

Wrong:

```jsx
case "LOGIN":

 fetch("/users");
```

---

Reducers must be:

```text
Pure Functions
```

---

No:

```text
API Calls

Timers

DOM Updates
```

---

Use:

```text
useEffect
```

instead.

---

# Pure Functions

---

# 41. What is Pure Function?

Same input:

```text
Same Output
```

---

Example:

```jsx
function add(a, b) {

 return a + b;
}
```

---

Pure.

---

Reducer should be:

```text
Pure
```

---

Always.

---

# Real World Example

---

# 42. Authentication System

State:

```jsx
{

 user: null,

 token: null,

 loading: false

}
```

---

Actions:

```text
LOGIN

LOGOUT

LOADING

ERROR
```

---

Reducer handles:

```text
All Auth Logic
```

---

Very common in enterprise projects.

---

# Best Practices

---

# 43. Use Constants

Instead of:

```jsx
"LOGIN"
```

---

Use:

```jsx
LOGIN
```

---

Avoid typos.

---

# 44. Keep Reducer Pure

No side effects.

---

Only:

```text
State Transformation
```

---

# 45. Split Large Reducers

Bad:

```text
1000 Line Reducer
```

---

Good:

```text
Auth Reducer

Cart Reducer

User Reducer
```

---

# 46. Use Context For Sharing

Common pattern:

```text
useReducer
 +
useContext
```

---

# useReducer vs Redux

---

# 47. Common Interview Question

Redux also uses reducers.

---

Similar concept.

---

Redux:

```text
Global State Library
```

---

useReducer:

```text
React Hook
```

---

Redux adds:

```text
Middleware

DevTools

Store

Advanced Features
```

---

# 48. Memory Trick

```text
useReducer
 ↓

Local Redux
```

---

Not technically identical.

---

But easy to remember.

---

# Interview Questions

---

### Q1. What is useReducer?

```text
Hook For Complex
State Management
```

---

### Q2. What does useReducer return?

```jsx
[
 state,

 dispatch
]
```

---

### Q3. What is a Reducer?

```text
Function That
Transforms State
```

---

### Q4. What is an Action?

```text
Object Describing
What Happened
```

---

### Q5. What is Dispatch?

```text
Function That Sends
Actions To Reducer
```

---

### Q6. When should you use useReducer?

```text
Complex State Logic
```

---

### Q7. Is Reducer Pure?

```text
Yes
```

---

Must be.

---

### Q8. Can Reducer Make API Calls?

```text
No
```

---

Use:

```text
useEffect
```

---

### Q9. Why useReducer + Context?

```text
Global State
Without Redux
```

---

### Q10. Difference Between useState and useReducer?

```text
Simple State

vs

Complex State
```

---

# Memory Tricks

### State

```text
Current Data
```

---

### Action

```text
What Happened
```

---

### Dispatch

```text
Send Action
```

---

### Reducer

```text
State Rules
```

---

### Formula

```text
State
  +
Action
  ↓

New State
```

---

### useState

```text
Simple State
```

---

### useReducer

```text
Complex State
```

---

### Context + Reducer

```text
Mini Redux
```

---

# Quick Revision

```text
useReducer
----------

Purpose:

Manage Complex State

Core Concepts:

1. State

2. Action

3. Dispatch

4. Reducer

Flow:

Dispatch
    ↓

Reducer
    ↓

New State
    ↓

Re-render

Action Example:

{
 type: "LOGIN"
}

Reducer Formula:

Current State
      +
Action
      ↓

New State

Use Cases:

✔ Authentication

✔ Shopping Cart

✔ Dashboard State

✔ Multi-Step Forms

Advantages:

✔ Centralized Logic

✔ Predictable Updates

✔ Scalable

✔ Easy Debugging

Disadvantages:

✘ More Boilerplate

✘ Overkill For Small State

Remember:

useState
 ↓

Simple State

useReducer
 ↓

Complex State

Reducer
 ↓

State Rules

Dispatch
 ↓

Send Action
```

---