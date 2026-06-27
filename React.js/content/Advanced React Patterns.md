# Advanced React Patterns

> **Goal:** Master Advanced React Patterns completely — understand why design patterns are needed in React, Higher Order Components (HOC), Render Props, Compound Components, Controlled Components Pattern, Custom Hooks Pattern, Provider Pattern, internal working, real-world examples, advantages, disadvantages, interview questions, and when to use each pattern.

---

# 1. Why React Patterns Exist?

As applications grow:

```text
More Components

More Reusable Logic

More State

More Communication
```

---

Code becomes:

```text
Duplicated

Hard To Maintain

Hard To Scale
```

---

Example:

```jsx
function UserList() {

 // fetch users

}

function ProductList() {

 // fetch products

}

function OrderList() {

 // fetch orders

}
```

---

Same logic repeated.

---

Need:

```text
Reusable Patterns
```

---

React Patterns solve:

```text
Code Reuse

Flexibility

Maintainability

Scalability
```

---

# Common Advanced React Patterns

```text
Higher Order Components

Render Props

Compound Components

Controlled Pattern

Custom Hooks Pattern

Provider Pattern
```

---

# Higher Order Components (HOC)

---

# 2. What is a Higher Order Component?

Most asked React interview topic.

---

Definition:

> A Higher Order Component (HOC) is a function that takes a component and returns a new enhanced component.

---

Simple Formula:

```text
Component
     ↓

HOC
     ↓

Enhanced Component
```

---

# Real-Life Analogy

Suppose:

```text
Coffee
```

---

Add:

```text
Milk
```

---

Result:

```text
Milk Coffee
```

---

Original object enhanced.

---

HOC does same.

---

# Syntax

```jsx
const EnhancedComponent =

hoc(
 Component
);
```

---

# Example

Normal component:

```jsx
function User(){

 return <h1>User</h1>;

}
```

---

HOC:

```jsx
function withLogger(

 WrappedComponent

){

 return function(){

  console.log(
   "Rendering"
  );

  return (
   <WrappedComponent />
  );

 };

}
```

---

Enhanced:

```jsx
const UserWithLogger =

withLogger(User);
```

---

Flow:

```text
User Component
       ↓

withLogger
       ↓

Enhanced User
```

---

# Real World Example

Authentication.

---

Without HOC:

```jsx
if(!user){

 return <Login />;
}
```

---

Repeated everywhere.

---

Create HOC:

```jsx
function withAuth(

 Component

){

 return function(props){

  if(!user){

   return <Login />;

  }

  return (

   <Component
    {...props}
   />

  );

 };

}
```

---

Usage:

```jsx
export default
withAuth(Dashboard);
```

---

Dashboard becomes protected.

---

# HOC Internal Flow

```text
Component
     ↓

Wrapped By HOC
     ↓

Additional Logic
     ↓

Render Original Component
```

---

# Advantages

✔ Reusable Logic

✔ Separation Of Concerns

✔ Cleaner Components

---

# Disadvantages

✘ Wrapper Hell

✘ Debugging Difficult

✘ Nested HOCs Become Complex

---

# Render Props

---

# 3. What are Render Props?

Before hooks existed:

```text
Logic Reuse Was Hard
```

---

Render Props solved this.

---

Definition:

> Render Props is a technique where a component shares logic by passing a function as a prop.

---

# Simple Idea

Instead of:

```text
Returning JSX
```

---

Return:

```text
Function
```

---

# Example

```jsx
<DataProvider

 render={(data)=>

  <h1>

   {data}

  </h1>

 }

/>
```

---

render prop:

```jsx
(data)=>...
```

---

Receives data.

---

# Implementation

```jsx
function DataProvider({

 render

}){

 const data =

 "React";

 return render(data);

}
```

---

Usage:

```jsx
<DataProvider

 render={(data)=>

  <h1>

   {data}

  </h1>

 }

/>
```

---

Output:

```text
React
```

---

# Internal Flow

```text
Parent
  ↓

Pass Function
  ↓

Child Executes Function
  ↓

Returns UI
```

---

# Mouse Tracker Example

Classic interview example.

---

Component:

```jsx
<MouseTracker

 render={(position)=>

  <h1>

   {position.x}

  </h1>

 }

/>
```

---

Logic reusable.

---

UI customizable.

---

# Render Props Advantages

✔ Logic Reuse

✔ Flexible UI

✔ Dynamic Rendering

---

# Disadvantages

✘ Nested Functions

✘ Callback Hell

✘ Hooks Mostly Replaced It

---

# HOC vs Render Props

---

# 4. Comparison

HOC:

```text
Component
 ↓

Enhanced Component
```

---

Render Props:

```text
Function
 ↓

Dynamic UI
```

---

Memory Trick:

```text
HOC
 ↓
 Wrap Component

Render Props
 ↓
 Pass Function
```

---

# Compound Components

---

# 5. What are Compound Components?

Used heavily in UI libraries.

Examples:

```text
Tabs

Accordions

Dropdowns

Menus
```

---

Definition:

> Compound Components are multiple components that work together and share state implicitly.

---

# Real Example

HTML:

```html
<select>

 <option>

 </option>

</select>
```

---

Option only works inside:

```text
Select
```

---

Compound relationship.

---

# React Example

```jsx
<Tabs>

 <Tabs.List>

  <Tabs.Tab />

 </Tabs.List>

 <Tabs.Panel />

</Tabs>
```

---

Components cooperate.

---

# Problem Without Compound Components

```jsx
<Tab1 />

<Tab2 />

<Panel />
```

---

Need manual state passing.

---

Messy.

---

# Compound Components Solution

Parent controls state.

---

Children access state automatically.

---

# Internal Architecture

```text
Parent Component
       ↓

Shared Context
       ↓

Children
```

---

Usually built using:

```text
Context API
```

---

# Example

```jsx
<Tabs>

 <Tab label="Home"/>

 <Tab label="Profile"/>

</Tabs>
```

---

All tabs share:

```text
Selected Tab State
```

---

Automatically.

---

# Advantages

✔ Clean API

✔ Better Reusability

✔ Natural Syntax

---

# Controlled Pattern

---

# 6. What is Controlled Pattern?

Very common.

---

React likes:

```text
State Controlled By Parent
```

---

Definition:

> Controlled Pattern means a component's state is managed externally by its parent.

---

# Example

```jsx
<input

 value={name}

 onChange={...}

/>
```

---

Input state controlled by React.

---

Not browser.

---

# Controlled Component

```jsx
function App(){

 const [

  value,

  setValue

 ]

 =
 useState("");

 return(

  <Input

   value={value}

   onChange={
    setValue
   }

  />

 );

}
```

---

Parent owns state.

---

# Benefits

```text
Single Source Of Truth
```

---

# Uncontrolled Version

```jsx
<input ref={ref}/>
```

---

Browser owns state.

---

# Controlled Pattern Flow

```text
Parent State
      ↓

Props
      ↓

Child Component
      ↓

Event
      ↓

Parent Update
```

---

# Custom Hooks Pattern

---

# 7. What is Custom Hooks Pattern?

One of the most important modern React patterns.

---

Problem:

Same logic repeated.

---

Example:

```jsx
useEffect()

fetch()

loading

error
```

---

Repeated everywhere.

---

Need reuse.

---

# Solution

Custom Hook.

---

Definition:

> Custom Hooks extract reusable stateful logic into reusable functions.

---

# Example

```jsx
function useCounter(){

 const [

  count,

  setCount

 ]

 =
 useState(0);

 const increment=()=>{

  setCount(
   c=>c+1
  );

 };

 return {

  count,

  increment

 };

}
```

---

Usage:

```jsx
const {

 count,

 increment

}

=
useCounter();
```

---

Reusable everywhere.

---

# Internal Flow

```text
Component
      ↓

Custom Hook
      ↓

React Hooks
      ↓

State Management
```

---

# Real World Examples

---

## Authentication

```jsx
useAuth()
```

---

## API Calls

```jsx
useFetch()
```

---

## Window Size

```jsx
useWindowSize()
```

---

## Theme

```jsx
useTheme()
```

---

# Advantages

✔ Logic Reuse

✔ Cleaner Components

✔ Easy Testing

✔ Modern React Standard

---

# Provider Pattern

---

# 8. What is Provider Pattern?

Very common in React ecosystem.

---

Examples:

```text
Context API

Redux

Theme Provider

React Query
```

---

Definition:

> Provider Pattern allows data to be shared with all descendant components without prop drilling.

---

# Problem

```text
App
 ↓

Header
 ↓

Navbar
 ↓

Profile
 ↓

Avatar
```

---

Need user data.

---

Without Provider:

```text
Pass Props Through
Every Component
```

---

Prop Drilling.

---

# Solution

Provider Pattern.

---

# Example

Create context:

```jsx
const UserContext =

createContext();
```

---

Provider:

```jsx
<UserContext.Provider

 value={user}

>

 <App />

</UserContext.Provider>
```

---

Consume:

```jsx
const user =

useContext(
 UserContext
);
```

---

No prop drilling.

---

# Internal Architecture

```text
Provider
    ↓

Context Value
    ↓

Child Components
```

---

# Real World Examples

---

## Theme Provider

```jsx
<ThemeProvider>

 <App/>

</ThemeProvider>
```

---

## Redux Provider

```jsx
<Provider store={store}>

 <App/>

</Provider>
```

---

## React Query

```jsx
<QueryClientProvider>

 <App/>

</QueryClientProvider>
```

---

# Provider Pattern Flow

```text
Provider
      ↓

Stores Value
      ↓

Children Subscribe
      ↓

Updates Propagate
```

---

# Comparison of All Patterns

| Pattern | Purpose |
|----------|----------|
| HOC | Reuse Logic By Wrapping Components |
| Render Props | Reuse Logic Using Functions |
| Compound Components | Components Work Together |
| Controlled Pattern | Parent Controls State |
| Custom Hooks | Reuse Stateful Logic |
| Provider Pattern | Share Global Data |

---

# Which Pattern Is Most Used Today?

Modern React:

```text
Custom Hooks

Context Provider

React Query

Redux Toolkit
```

---

Less common today:

```text
HOC

Render Props
```

---

Still important for interviews.

---

# Real World Example

Suppose:

```text
Authentication System
```

---

Provider Pattern:

```jsx
<AuthProvider>
```

---

Custom Hook:

```jsx
useAuth()
```

---

Controlled Pattern:

```jsx
<LoginForm />
```

---

Compound Component:

```jsx
<Modal>
 <Modal.Header/>
 <Modal.Body/>
</Modal>
```

---

Together create scalable architecture.

---

# Common Mistakes

---

## Using HOCs Excessively

Creates:

```text
Wrapper Hell
```

---

## Using Context For Everything

Can cause:

```text
Unnecessary Re-renders
```

---

## Creating Huge Custom Hooks

Keep hooks focused.

---

## Mixing Controlled and Uncontrolled Inputs

Can create warnings.

---

# Interview Questions

---

### Q1. What is a Higher Order Component?

```text
Function That Takes
A Component And Returns
An Enhanced Component
```

---

### Q2. What problem does Render Props solve?

```text
Logic Reuse
```

---

### Q3. What replaced Render Props mostly?

```text
Custom Hooks
```

---

### Q4. What are Compound Components?

```text
Components That Work
Together Using Shared State
```

---

### Q5. What is a Controlled Component?

```text
Parent Controls State
```

---

### Q6. What is a Custom Hook?

```text
Reusable Stateful Logic
```

---

### Q7. Why use Provider Pattern?

```text
Avoid Prop Drilling
```

---

### Q8. Give examples of Provider Pattern.

```text
Context API

Redux Provider

Theme Provider
```

---

### Q9. HOC vs Custom Hook?

HOC:

```text
Wrap Component
```

---

Custom Hook:

```text
Reuse Logic Directly
```

---

### Q10. Which pattern is most used in modern React?

```text
Custom Hooks
```

and

```text
Provider Pattern
```

---

# Memory Tricks

### HOC

```text
Wrap Component
```

---

### Render Props

```text
Pass Function
```

---

### Compound Components

```text
Components Work Together
```

---

### Controlled Pattern

```text
Parent Owns State
```

---

### Custom Hooks

```text
Reuse Logic
```

---

### Provider Pattern

```text
Share Data Globally
```

---

# Complete Pattern Flow

```text
HOC
 ↓
 Enhance Component

Render Props
 ↓
 Share Logic Through Function

Compound Components
 ↓
 Shared State Components

Controlled Pattern
 ↓
 Parent Controls Child

Custom Hooks
 ↓
 Reusable Logic

Provider Pattern
 ↓
 Global Data Sharing
```

---

# Quick Revision

```text
Advanced React Patterns
-----------------------

HOC
 ↓
 Wrap Component

Render Props
 ↓
 Function As Prop

Compound Components
 ↓
 Shared State Components

Controlled Pattern
 ↓
 Parent Controls State

Custom Hooks
 ↓
 Reuse Stateful Logic

Provider Pattern
 ↓
 Avoid Prop Drilling

Most Modern:

✔ Custom Hooks

✔ Provider Pattern

✔ Context API

✔ React Query

Less Common:

HOC

Render Props

Remember:

HOC
 ↓
 Enhance Component

Custom Hook
 ↓
 Reuse Logic

Provider
 ↓
 Share Data
```

---