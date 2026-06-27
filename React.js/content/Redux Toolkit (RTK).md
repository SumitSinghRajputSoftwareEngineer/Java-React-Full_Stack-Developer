# Redux Toolkit (RTK)

> **Goal:** Master Redux Toolkit (RTK) completely — understand why RTK was created, how it solves Redux problems, `configureStore`, `createSlice`, `createAsyncThunk`, RTK Query, internal working, async data fetching, API caching, best practices, performance benefits, and interview questions.

---

# 1. What is Redux Toolkit (RTK)?

Redux Toolkit is:

```text
Official Redux Toolset
```

---

Created by:

```text
Redux Team
```

---

Purpose:

```text
Simplify Redux Development
```

---

Official recommendation:

```text
Use RTK Instead Of Plain Redux
```

---

# Definition

> Redux Toolkit is the official, opinionated, and recommended way to write Redux applications with less boilerplate and better developer experience.

---

# Why RTK Was Created?

---

# 2. Problem With Traditional Redux

Traditional Redux requires:

```text
Store

Actions

Action Types

Reducers

Switch Statements

Middleware Configuration
```

---

Simple Counter:

```text
Many Files
```

---

Example:

```text
actions.js

reducers.js

store.js

constants.js
```

---

Too much boilerplate.

---

Developers complained.

---

Need:

```text
Less Code

Cleaner Architecture

Better Defaults
```

---

RTK solves this.

---

# Traditional Redux Example

Action:

```jsx
{
 type:"INCREMENT"
}
```

---

Reducer:

```jsx
switch(action.type){

 case "INCREMENT":
   return state + 1;

 default:
   return state;
}
```

---

RTK removes most of this.

---

# RTK Features

---

# 3. Major Features

RTK provides:

```text
configureStore

createSlice

createAsyncThunk

RTK Query
```

---

Benefits:

```text
Less Boilerplate

Built-in Immer

Built-in Redux Thunk

Better Performance

Simpler Async Logic
```

---

# RTK Architecture

```text
Component
      ↓

Dispatch
      ↓

Slice
      ↓

Store
      ↓

UI Updates
```

---

Much cleaner than classic Redux.

---

# configureStore

---

# 4. What is configureStore?

Creates Redux store.

---

Modern replacement for:

```jsx
createStore()
```

---

Recommended by Redux team.

---

# Import

```jsx
import {

 configureStore

}
from
"@reduxjs/toolkit";
```

---

# Basic Example

```jsx
const store =

configureStore({

 reducer:{}

});
```

---

Store created.

---

# Why Better Than createStore?

Automatically configures:

```text
Redux DevTools

Redux Thunk

Middleware

Performance Checks
```

---

No manual setup.

---

# Traditional Redux

```jsx
createStore(

 reducer,

 applyMiddleware(
  thunk
 )

);
```

---

More code.

---

# RTK

```jsx
configureStore({

 reducer

});
```

---

Much cleaner.

---

# Example

```jsx
const store =

configureStore({

 reducer:{

  counter:
  counterReducer

 }

});
```

---

State:

```jsx
{
 counter:{}
}
```

---

Stored inside Redux.

---

# Internal Working

---

# 5. What Happens Internally?

RTK automatically:

```text
Creates Store

Adds Middleware

Enables DevTools

Configures Enhancers
```

---

Developer writes:

```text
Minimal Code
```

---

# createSlice

---

# 6. What is createSlice?

Most important RTK feature.

---

Creates:

```text
Reducer

Actions

Action Types
```

---

Automatically.

---

Before RTK:

```text
Action Types

Actions

Reducers

All Separate
```

---

After RTK:

```text
Single Slice
```

---

# Import

```jsx
import {

 createSlice

}
from
"@reduxjs/toolkit";
```

---

# Basic Example

```jsx
const counterSlice =

createSlice({

 name:"counter",

 initialState:{
  count:0
 },

 reducers:{}

});
```

---

Creates:

```text
Reducer

Actions
```

Automatically.

---

# Components of createSlice

---

# 7. Slice Structure

```jsx
createSlice({

 name,

 initialState,

 reducers

});
```

---

Contains:

```text
Name

Initial State

Reducer Functions
```

---

# Example

```jsx
const counterSlice =

createSlice({

 name:"counter",

 initialState:{

  count:0

 },

 reducers:{

  increment(){},

  decrement(){}

 }

});
```

---

# name

Used for:

```text
Action Type Prefix
```

---

Example:

```text
counter/increment
```

---

Automatically generated.

---

# initialState

Initial store data.

---

Example:

```jsx
{
 count:0
}
```

---

# reducers

Contains reducer functions.

---

# Example

```jsx
reducers:{

 increment(state){

  state.count++;

 }

}
```

---

Notice:

```jsx
state.count++;
```

---

Looks wrong?

---

Actually valid in RTK.

---

Why?

---

# Immer

---

# 8. What is Immer?

RTK internally uses:

```text
Immer Library
```

---

Immer allows:

```jsx
state.count++;
```

---

While maintaining:

```text
Immutability
```

---

Internally:

```text
Creates Copy

Updates Copy

Returns New State
```

---

Developer writes:

```text
Mutable Looking Code
```

---

Redux remains immutable.

---

# Example

Traditional Redux:

```jsx
return {

 ...state,

 count:
 state.count + 1

};
```

---

RTK:

```jsx
state.count++;
```

---

Much simpler.

---

# Exporting Actions

---

# 9. Generated Actions

RTK automatically creates actions.

---

Example:

```jsx
const counterSlice =

createSlice({

 reducers:{

  increment(state){

   state.count++;

  }

 }

});
```

---

Export:

```jsx
export const {

 increment

}
=
counterSlice.actions;
```

---

Action automatically generated.

---

No need to write:

```jsx
{
 type:"INCREMENT"
}
```

---

# Export Reducer

```jsx
export default

counterSlice.reducer;
```

---

Store uses reducer.

---

# Complete Counter Example

---

# 10. Slice

```jsx
const counterSlice =

createSlice({

 name:"counter",

 initialState:{

  count:0

 },

 reducers:{

  increment(state){

   state.count++;

  },

  decrement(state){

   state.count--;

  }

 }

});
```

---

Dispatch:

```jsx
dispatch(
 increment()
);
```

---

State updates automatically.

---

# createAsyncThunk

---

# 11. Why createAsyncThunk?

Redux reducers must be:

```text
Pure
```

---

Cannot perform:

```text
API Calls
```

---

Need async mechanism.

---

RTK provides:

```text
createAsyncThunk
```

---

Purpose:

```text
Handle Async Logic
```

---

Examples:

```text
Fetch Users

Login User

Load Products

Load Orders
```

---

# Import

```jsx
import {

 createAsyncThunk

}
from
"@reduxjs/toolkit";
```

---

# Basic Example

```jsx
export const fetchUsers =

createAsyncThunk(

 "users/fetchUsers",

 async () => {

  const response =

  await axios.get(
   "/users"
  );

  return response.data;

 }

);
```

---

# Internal Lifecycle

When dispatched:

```jsx
dispatch(
 fetchUsers()
);
```

---

RTK automatically creates:

```text
Pending

Fulfilled

Rejected
```

---

# Visualization

```text
Dispatch
     ↓

Pending
     ↓

API Call
     ↓

Success
     ↓

Fulfilled
```

---

OR

```text
Dispatch
     ↓

Pending
     ↓

API Call
     ↓

Failure
     ↓

Rejected
```

---

# Generated Actions

RTK automatically creates:

```text
users/fetchUsers/pending

users/fetchUsers/fulfilled

users/fetchUsers/rejected
```

---

No manual coding.

---

# Handling Async States

---

# 12. Extra Reducers

Used for:

```text
Async Actions
```

---

Example:

```jsx
extraReducers:(builder)=>{

}
```

---

# Example

```jsx
extraReducers:(builder)=>{

 builder.addCase(

  fetchUsers.pending,

  (state)=>{

   state.loading=true;

  }

 );

}
```

---

# Fulfilled

```jsx
builder.addCase(

 fetchUsers.fulfilled,

 (state,action)=>{

  state.loading=false;

  state.users=
  action.payload;

 }

);
```

---

# Rejected

```jsx
builder.addCase(

 fetchUsers.rejected,

 (state)=>{

  state.loading=false;

 }

);
```

---

Flow:

```text
Pending
 ↓

Loading=true

Fulfilled
 ↓

Data Loaded

Rejected
 ↓

Error State
```

---

# Real World Example

---

# 13. Load Products

```jsx
const fetchProducts =

createAsyncThunk(

 "products/load",

 async ()=>{

  const response =

  await axios.get(
   "/products"
  );

  return response.data;

 }

);
```

---

Component:

```jsx
dispatch(
 fetchProducts()
);
```

---

RTK handles:

```text
Loading

Success

Failure
```

---

Automatically.

---

# RTK Query

---

# 14. What is RTK Query?

Newest Redux feature.

---

Purpose:

```text
API Data Fetching
```

---

Automatically handles:

```text
Caching

Loading

Errors

Refetching

Invalidation
```

---

Without RTK Query:

```text
Axios

createAsyncThunk

Loading States

Error States
```

---

Many lines of code.

---

RTK Query reduces all this.

---

# Definition

> RTK Query is a powerful data fetching and caching tool built on top of Redux Toolkit.

---

# Traditional Flow

```text
Dispatch
 ↓

Thunk
 ↓

Axios
 ↓

Reducer
 ↓

Store
```

---

RTK Query:

```text
Single Hook
```

---

Much simpler.

---

# Creating API

---

# 15. createApi()

RTK Query starts with:

```jsx
createApi()
```

---

Example:

```jsx
import {

 createApi,

 fetchBaseQuery

}
from
"@reduxjs/toolkit/query/react";
```

---

# Example

```jsx
export const api =

createApi({

 reducerPath:"api",

 baseQuery:

 fetchBaseQuery({

  baseUrl:
  "/api"

 }),

 endpoints:()=>({})

});
```

---

Creates API service.

---

# Defining Endpoints

---

# 16. Example

```jsx
endpoints:(builder)=>({

 getUsers:

 builder.query({

  query:()=>"/users"

 })

})
```

---

Defines:

```text
GET /users
```

---

Endpoint.

---

# Generated Hook

RTK automatically creates:

```jsx
useGetUsersQuery()
```

---

Amazing feature.

---

# Using Query Hook

---

# 17. Example

```jsx
const {

 data,

 error,

 isLoading

}

=
useGetUsersQuery();
```

---

Automatically provides:

```text
Data

Loading

Error
```

---

No need:

```jsx
useEffect()

useState()

Dispatch()
```

---

# Mutation

---

# 18. What is Mutation?

Used for:

```text
POST

PUT

PATCH

DELETE
```

---

Example:

```jsx
addUser:

builder.mutation({

 query:(user)=>({

  url:"/users",

  method:"POST",

  body:user

 })

})
```

---

Generated hook:

```jsx
useAddUserMutation()
```

---

Usage:

```jsx
const [

 addUser

]

=
useAddUserMutation();
```

---

Call:

```jsx
addUser(user);
```

---

# RTK Query Caching

---

# 19. Biggest Feature

Suppose:

```text
Users Loaded Once
```

---

Navigate:

```text
Page A → Page B → Page A
```

---

RTK Query:

```text
Uses Cache
```

---

No duplicate API calls.

---

Performance improves significantly.

---

# Automatic Refetching

---

# 20. RTK Query Can

Automatically:

```text
Refetch Data

Invalidate Cache

Sync UI
```

---

Example:

```text
New Product Added
```

---

Product list refreshes automatically.

---

# RTK vs createAsyncThunk

---

# 21. createAsyncThunk

Good for:

```text
Complex Business Logic
```

---

Manual state handling.

---

# RTK Query

Good for:

```text
API Calls
```

---

Automatic handling.

---

# Comparison

| Feature | createAsyncThunk | RTK Query |
|----------|----------|----------|
| API Calls | Yes | Yes |
| Caching | No | Yes |
| Auto Refetch | No | Yes |
| Loading State | Manual | Automatic |
| Error State | Manual | Automatic |
| Boilerplate | Medium | Very Low |

---

# Complete RTK Architecture

---

# 22. Flow

```text
Component
     ↓

Dispatch Action
     ↓

Slice Reducer
     ↓

Store
     ↓

UI Update
```

---

Async Flow:

```text
Component
      ↓

createAsyncThunk
      ↓

Pending
      ↓

API
      ↓

Fulfilled/Rejected
      ↓

Store Update
```

---

RTK Query Flow:

```text
Hook
 ↓

API Call
 ↓

Cache
 ↓

Store
 ↓

UI
```

---

# Common Mistakes

---

# 23. Mutating State Outside Slice

Wrong:

```jsx
store.user.name=
"John";
```

---

Always:

```text
Dispatch Actions
```

---

# 24. Using createAsyncThunk For Every API

Not always ideal.

---

For CRUD APIs:

```text
Prefer RTK Query
```

---

# 25. Not Using RTK

Modern Redux apps should use:

```text
Redux Toolkit
```

---

Not plain Redux.

---

# Best Practices

---

# 26. Use createSlice For All State

Avoid manual reducers.

---

# 27. Use createAsyncThunk For Complex Async Logic

Examples:

```text
Login Flow

Multi-Step APIs

Business Logic
```

---

# 28. Use RTK Query For API Management

Provides:

```text
Caching

Refetching

Loading

Errors
```

---

Automatically.

---

# 29. Keep Slices Small

Examples:

```text
Auth Slice

Cart Slice

User Slice

Theme Slice
```

---

# Interview Questions

---

### Q1. What is Redux Toolkit?

```text
Official Redux Toolset
```

---

### Q2. Why RTK Was Created?

```text
Reduce Redux Boilerplate
```

---

### Q3. What does configureStore do?

```text
Creates Redux Store
```

---

### Q4. What does createSlice do?

```text
Creates Reducers And Actions
Automatically
```

---

### Q5. What library allows mutation-like syntax?

```text
Immer
```

---

### Q6. What does createAsyncThunk do?

```text
Handles Async Logic
```

---

### Q7. What actions are generated by createAsyncThunk?

```text
Pending

Fulfilled

Rejected
```

---

### Q8. What is RTK Query?

```text
API Fetching And Caching Tool
```

---

### Q9. RTK Query vs createAsyncThunk?

RTK Query:

```text
API Management
```

---

createAsyncThunk:

```text
Custom Async Logic
```

---

### Q10. What is the biggest RTK Query feature?

```text
Automatic Caching
```

---

# Memory Tricks

### configureStore

```text
Create Store
```

---

### createSlice

```text
Create Reducers + Actions
```

---

### Immer

```text
Mutation-Like Syntax
```

---

### createAsyncThunk

```text
Async Operations
```

---

### Pending

```text
Loading
```

---

### Fulfilled

```text
Success
```

---

### Rejected

```text
Failure
```

---

### RTK Query

```text
API + Cache
```

---

# Complete RTK Flow

```text
configureStore
        ↓

createSlice
        ↓

Actions Generated
        ↓

Dispatch
        ↓

Reducer
        ↓

Store
        ↓

UI Update

Async:

createAsyncThunk
        ↓

Pending
        ↓

Fulfilled
OR
Rejected

RTK Query:

Hook
 ↓

API
 ↓

Cache
 ↓

UI
```

---

# Quick Revision

```text
Redux Toolkit (RTK)
-------------------

Purpose:
Simplify Redux

Core Features:

configureStore
 ↓
 Create Store

createSlice
 ↓
 Reducers + Actions

createAsyncThunk
 ↓
 Async Logic

RTK Query
 ↓
 API Fetching + Caching

Benefits:

✔ Less Boilerplate

✔ Built-in Immer

✔ Built-in Thunk

✔ DevTools Support

✔ Automatic Caching

Remember:

Redux
 ↓
 State Management

RTK
 ↓
 Easier Redux

RTK Query
 ↓
 API Management
```

---