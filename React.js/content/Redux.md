# Redux

> **Goal:** Master Redux from scratch — understand why Redux exists, Redux Fundamentals, Store, Actions, Reducers, Dispatch, Middleware, Redux DevTools, Internal Working, Data Flow, Real-World Examples, Best Practices, and Interview Questions.

---

# 1. What is Redux?

Redux is a:

```text
State Management Library
```

---

Used to manage:

```text
Application State
```

---

In one central location.

---

Example:

```text
Shopping Cart

Logged User

Theme

Notifications

Orders
```

---

Instead of storing state in many components:

```text
Store State Centrally
```

---

This central storage is called:

```text
Redux Store
```

---

# Definition

> Redux is a predictable state management library that stores application state in a centralized store and updates it through actions and reducers.

---

# 2. Why Redux?

Suppose application structure:

```text
App
 ↓

Navbar

Products

Cart

Profile

Orders
```

---

Need:

```text
Cart Count
```

---

Without Redux:

```text
Pass Props Everywhere
```

---

Problem:

```text
Prop Drilling

State Duplication

Complex Communication
```

---

Application becomes difficult to maintain.

---

Need:

```text
Single Source Of Truth
```

---

Redux solves this.

---

# Traditional React State

```text
Parent
 ↓

Child A

Child B

Child C
```

---

State lifted up.

---

Works for small apps.

---

Not ideal for:

```text
Large Applications
```

---

# Redux Solution

```text
Redux Store

 ↓

Navbar

Cart

Orders

Profile
```

---

Everyone accesses same state.

---

# 3. Redux Core Principles

Redux follows three principles.

---

## Principle 1

### Single Source Of Truth

Application state stored in:

```text
One Store
```

---

Example:

```text
Store
```

contains:

```text
User

Cart

Products

Theme
```

---

# Principle 2

### State Is Read Only

Cannot directly modify state.

---

Wrong:

```jsx
state.count++;
```

---

Redux forbids this.

---

Need:

```text
Action
```

---

# Principle 3

### Changes Through Reducers

State changes only through:

```text
Reducers
```

---

Visualization:

```text
Action
   ↓

Reducer
   ↓

New State
```

---

Predictable.

---

# Redux Architecture

---

# 4. Complete Redux Flow

```text
Component
     ↓

Dispatch Action
     ↓

Store
     ↓

Reducer
     ↓

New State
     ↓

UI Re-render
```

---

Very important.

---

# Real Example

User clicks:

```text
Add To Cart
```

---

Flow:

```text
Button Click
      ↓

Dispatch Action
      ↓

Reducer Updates Cart
      ↓

Store Updated
      ↓

Navbar Count Updated
```

---

# Redux Components

Redux consists of:

```text
Store

Actions

Reducers

Dispatch

Middleware
```

---

# Visualization

```text
Component
    ↓

Dispatch
    ↓

Action
    ↓

Reducer
    ↓

Store
    ↓

UI Update
```

---

# Store

---

# 5. What is Store?

Store is:

```text
Central State Container
```

---

Contains entire application state.

---

Example:

```jsx
{
 user:{},

 cart:[],

 theme:"dark"
}
```

---

All application data lives here.

---

# Visualization

```text
Store

 ├ User

 ├ Cart

 ├ Orders

 ├ Theme
```

---

Single source of truth.

---

# Creating Store

Old Redux:

```jsx
createStore()
```

---

Modern Redux:

```jsx
configureStore()
```

---

Example:

```jsx
import {

 configureStore

}
from
"@reduxjs/toolkit";
```

---

```jsx
const store =

configureStore({

 reducer: rootReducer

});
```

---

# Store Responsibilities

Store:

```text
Holds State

Receives Actions

Calls Reducers

Notifies Components
```

---

# Actions

---

# 6. What is an Action?

Action describes:

```text
What Happened
```

---

Action does NOT update state.

---

Action only describes event.

---

Example:

```text
Add Item

Remove Item

Login User
```

---

# Action Object

```jsx
{
 type:"ADD_TO_CART"
}
```

---

Every action requires:

```text
type
```

---

# Example

```jsx
{
 type:"INCREMENT"
}
```

---

Meaning:

```text
Increase Counter
```

---

# Action With Payload

Often need extra data.

---

Example:

```jsx
{
 type:"ADD_PRODUCT",

 payload:{
  id:1,
  name:"Laptop"
 }
}
```

---

Payload contains:

```text
Additional Information
```

---

# Real World Examples

```jsx
{
 type:"LOGIN"
}
```

---

```jsx
{
 type:"LOGOUT"
}
```

---

```jsx
{
 type:"ADD_TO_CART"
}
```

---

```jsx
{
 type:"DELETE_PRODUCT"
}
```

---

# Action Creators

---

# 7. What are Action Creators?

Functions that create actions.

---

Example:

```jsx
function increment() {

 return {

  type:"INCREMENT"

 };

}
```

---

Usage:

```jsx
dispatch(
 increment()
);
```

---

Cleaner.

---

# Reducers

---

# 8. What is a Reducer?

Reducer decides:

```text
How State Changes
```

---

Definition:

> Reducer is a pure function that receives current state and action, then returns new state.

---

Syntax:

```jsx
(state, action)
      ↓
returns
newState
```

---

# Example

```jsx
function counterReducer(

 state = 0,

 action

) {

 switch(action.type){

  case "INCREMENT":

   return state + 1;

  default:

   return state;

 }

}
```

---

# Internal Flow

Action:

```jsx
{
 type:"INCREMENT"
}
```

---

Reducer receives:

```text
Current State

Action
```

---

Returns:

```text
Updated State
```

---

# Visualization

```text
Current State = 0
        ↓

INCREMENT
        ↓

Reducer
        ↓

New State = 1
```

---

# Important Rule

Reducers must be:

```text
Pure Functions
```

---

Meaning:

```text
No API Calls

No Side Effects

No State Mutation
```

---

# Wrong

```jsx
state.count++;
```

---

# Correct

```jsx
return {

 ...state,

 count:

 state.count + 1

};
```

---

# Dispatch

---

# 9. What is Dispatch?

Dispatch sends action to Redux.

---

Think:

```text
Trigger State Update
```

---

Syntax:

```jsx
dispatch(action);
```

---

Example:

```jsx
dispatch({

 type:"INCREMENT"

});
```

---

Flow:

```text
Dispatch
     ↓

Store
     ↓

Reducer
     ↓

New State
```

---

# Example

```jsx
<button

 onClick={() =>

  dispatch({

   type:"INCREMENT"

  })

 }

>

 Increase

</button>
```

---

User clicks.

---

Dispatch executes.

---

State changes.

---

# Complete Flow Example

---

# 10. Counter Example

Initial State:

```jsx
{
 count:0
}
```

---

Dispatch:

```jsx
dispatch({

 type:"INCREMENT"

});
```

---

Reducer:

```jsx
return {

 count:

 state.count + 1

};
```

---

New State:

```jsx
{
 count:1
}
```

---

Component re-renders.

---

# Middleware

---

# 11. What is Middleware?

Middleware sits between:

```text
Dispatch

and

Reducer
```

---

Visualization:

```text
Dispatch
    ↓

Middleware
    ↓

Reducer
```

---

Acts like:

```text
Interceptor
```

---

Can:

```text
Log Actions

Handle Async Calls

Modify Actions

Perform Side Effects
```

---

# Why Middleware?

Reducers must be:

```text
Pure
```

---

Cannot perform:

```text
API Calls
```

---

Need middleware.

---

# Example

```text
Login Button
      ↓

API Call
      ↓

Success
      ↓

Update Redux State
```

---

Middleware handles API.

---

# Logging Middleware

Example:

```jsx
const logger =

store =>

next =>

action => {

 console.log(action);

 return next(action);

};
```

---

Logs every action.

---

# Redux Thunk

---

# 12. Most Popular Middleware

```text
Redux Thunk
```

---

Allows:

```text
Async Actions
```

---

Example:

```jsx
async function fetchUsers() {

 const users =

 await api.get();

}
```

---

Without thunk:

```text
Not Possible Directly
```

---

Thunk enables it.

---

# Example

```jsx
export const loadUsers =
() => async (dispatch) => {

 const response =

 await axios.get(
  "/users"
 );

 dispatch({

  type:"SET_USERS",

  payload:
  response.data

 });

};
```

---

# Flow

```text
Dispatch
     ↓

Thunk
     ↓

API Call
     ↓

Success
     ↓

Dispatch New Action
```

---

# Redux DevTools

---

# 13. What is Redux DevTools?

Browser extension.

---

Used to inspect Redux.

---

Shows:

```text
Actions

State Changes

Payloads

History
```

---

Most loved Redux feature.

---

# Visualization

```text
Action History

INCREMENT

INCREMENT

ADD_TO_CART

LOGIN
```

---

Can inspect every action.

---

# Time Travel Debugging

---

# 14. Amazing Redux Feature

Redux DevTools can:

```text
Go Back In Time
```

---

Example:

```text
Action 1

Action 2

Action 3

Action 4
```

---

Move back:

```text
Action 2
```

---

Application state restored.

---

Very useful debugging tool.

---

# Internal Redux Workflow

---

# 15. Full Lifecycle

User Clicks:

```text
Add To Cart
```

---

Flow:

```text
Dispatch Action
       ↓

Middleware
       ↓

Reducer
       ↓

Store Updated
       ↓

Subscribers Notified
       ↓

UI Re-render
```

---

# Redux Data Flow

---

# 16. Why Redux Is Predictable?

Because flow is:

```text
One Direction
```

---

Visualization:

```text
UI
 ↓

Action
 ↓

Reducer
 ↓

Store
 ↓

UI
```

---

Never:

```text
Store Directly Changes UI
```

---

Predictable updates.

---

# Redux vs Context API

---

# 17. Context API

Good for:

```text
Theme

Language

Authentication
```

---

Small-to-medium applications.

---

# Redux

Good for:

```text
Complex State

Large Applications

Frequent Updates
```

---

Examples:

```text
E-Commerce

Banking

Enterprise Systems
```

---

# Comparison

| Feature | Context API | Redux |
|----------|----------|----------|
| Built Into React | Yes | No |
| Easy Setup | Yes | Medium |
| DevTools | Limited | Excellent |
| Middleware | No | Yes |
| Large Apps | Medium | Excellent |
| Predictable State | Medium | Excellent |

---

# Common Mistakes

---

# 18. Mutating State

Wrong:

```jsx
state.count++;
```

---

Redux cannot track changes properly.

---

Correct:

```jsx
return {

 ...state,

 count:
 state.count + 1

};
```

---

# 19. Putting API Calls Inside Reducers

Wrong:

```jsx
function reducer(){

 fetch(...);

}
```

---

Reducers must be:

```text
Pure
```

---

Use:

```text
Middleware
```

---

# 20. Creating Too Many Stores

Wrong:

```text
Store 1

Store 2

Store 3
```

---

Redux principle:

```text
Single Store
```

---

# Best Practices

---

# 21. Keep Reducers Pure

No:

```text
API Calls

Random Values

Date Manipulation
```

---

Inside reducers.

---

# 22. Use Redux Toolkit

Modern Redux standard.

---

Provides:

```text
Less Boilerplate

Cleaner Code
```

---

# 23. Use Middleware For Async Operations

Examples:

```text
API Calls

Authentication

Logging
```

---

# 24. Install Redux DevTools

Improves debugging tremendously.

---

# Interview Questions

---

### Q1. What is Redux?

```text
Centralized State
Management Library
```

---

### Q2. What problem does Redux solve?

```text
Complex State Sharing
```

---

### Q3. What is Store?

```text
Central State Container
```

---

### Q4. What is an Action?

```text
Describes What Happened
```

---

### Q5. What is a Reducer?

```text
Function That Updates State
```

---

### Q6. What is Dispatch?

```text
Sends Action To Store
```

---

### Q7. Why Middleware?

```text
Handle Side Effects
And Async Logic
```

---

### Q8. What is Redux Thunk?

```text
Middleware For Async Actions
```

---

### Q9. What is Redux DevTools?

```text
Redux Debugging Tool
```

---

### Q10. Why Are Reducers Pure?

```text
Predictable State Updates
```

---

# Memory Tricks

### Store

```text
State Storage
```

---

### Action

```text
What Happened
```

---

### Reducer

```text
How State Changes
```

---

### Dispatch

```text
Send Action
```

---

### Middleware

```text
Intercept Action
```

---

### Redux DevTools

```text
Debug Redux
```

---

### Redux Flow

```text
UI
 ↓

Dispatch
 ↓

Action
 ↓

Middleware
 ↓

Reducer
 ↓

Store
 ↓

UI
```

---

# Complete Redux Architecture

```text
Component
      ↓

Dispatch
      ↓

Action
      ↓

Middleware
      ↓

Reducer
      ↓

Store
      ↓

Subscriber
      ↓

UI Re-render
```

---

# Quick Revision

```text
Redux
------

Purpose:
Centralized State Management

Core Components:

Store
 ↓ State Storage

Actions
 ↓ What Happened

Reducers
 ↓ State Update Logic

Dispatch
 ↓ Send Actions

Middleware
 ↓ Async Logic

Redux DevTools
 ↓ Debugging

Key Principle:

Single Source Of Truth

Redux Flow:

UI
 ↓

Action
 ↓

Reducer
 ↓

Store
 ↓

UI

Remember:

Action
 ↓
What Happened

Reducer
 ↓
How State Changes

Store
 ↓
Where State Lives
```

