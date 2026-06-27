# Virtual DOM

> One of the most asked React interview topics. Every React developer should understand not just the definition, but also how it works internally.

---

# What is Virtual DOM?

Virtual DOM (VDOM) is a lightweight JavaScript representation of the Real DOM.

---

Real DOM:

```html
<div>
    <h1>Hello</h1>
</div>
```

---

Virtual DOM:

```javascript
{
  type: "div",
  props: {
    children: [
      {
        type: "h1",
        props: {
          children: "Hello"
        }
      }
    ]
  }
}
```

---

React does NOT directly update the browser DOM.

Instead:

```text
State Change
      ↓
Create New Virtual DOM
      ↓
Compare With Previous VDOM
      ↓
Find Differences
      ↓
Update Real DOM
```

---

# Why Virtual DOM?

Direct DOM manipulation is expensive.

Example:

```javascript
document.getElementById("title")
        .innerText = "New Title";
```

---

For small apps:

```text
Fine
```

---

For large applications:

```text
Thousands of DOM Nodes
```

---

Frequent updates become slow.

---

React solves this by:

```text
Updating Only Changed Parts
```

---

# Virtual DOM Flow

```text
Initial Render

Component
    ↓

Virtual DOM
    ↓

Real DOM
```

---

State Changes:

```text
New State
     ↓

New Virtual DOM
     ↓

Diffing
     ↓

Patch Real DOM
```

---

# Example

Initial:

```jsx
<h1>Hello</h1>
```

---

After update:

```jsx
<h1>Hello React</h1>
```

---

React compares:

```text
Hello
```

with

```text
Hello React
```

---

Only text node changes.

---

Entire page is NOT recreated.

---

# Advantages

✔ Faster updates

✔ Better performance

✔ Declarative UI

✔ Simplified development

---

# Disadvantages

✘ Additional memory usage

✘ Comparison overhead

---

# Interview Question

### Does React update the entire DOM?

```text
No
```

---

Only changed parts are updated.

---

# Memory Trick

```text
Real DOM
     ↓
Expensive

Virtual DOM
     ↓
Cheap Copy
```

---

# Reconciliation Algorithm

> Reconciliation is React's process of determining what changed between two Virtual DOM trees.

---

# Definition

Whenever state changes:

```text
Old Virtual DOM
      ↓

New Virtual DOM
```

---

React compares both trees.

This process is called:

```text
Reconciliation
```

---

# Goal

Determine:

```text
What Changed?
```

---

Without:

```text
Re-rendering Everything
```

---

# Example

Old:

```jsx
<h1>Hello</h1>
```

---

New:

```jsx
<h1>Hello React</h1>
```

---

React finds:

```text
Only Text Changed
```

---

Updates only text node.

---

# Reconciliation Flow

```text
State Change
      ↓

New Virtual DOM
      ↓

Compare Trees
      ↓

Generate Changes
      ↓

Update DOM
```

---

# Why Important?

Without reconciliation:

```text
Destroy Entire DOM
Create Again
```

---

Very expensive.

---

# Interview Question

### What is Reconciliation?

```text
React's process of comparing old and new Virtual DOM trees to determine minimal updates.
```

---

# Diffing Algorithm

> Diffing is the actual comparison algorithm used during reconciliation.

---

# Problem

Comparing two trees can be expensive.

---

Generic tree comparison:

```text
O(n³)
```

---

Very slow.

---

React uses optimized diffing:

```text
O(n)
```

---

# React Assumptions

---

## Assumption 1

Different element types:

```jsx
<div>
```

vs

```jsx
<span>
```

---

Means:

```text
Completely Different Tree
```

---

React destroys old tree.

---

## Assumption 2

Keys identify stable elements.

---

Example:

```jsx
users.map(user =>
    <li key={user.id}>
        {user.name}
    </li>
)
```

---

React knows:

```text
Which Item Changed
```

---

# Example

Old:

```jsx
<li>A</li>
<li>B</li>
<li>C</li>
```

---

New:

```jsx
<li>A</li>
<li>B</li>
<li>D</li>
```

---

React updates:

```text
Only C → D
```

---

# Interview Question

### What algorithm does React use?

```text
Optimized O(n) Diffing Algorithm
```

---

# React Fiber

> React Fiber is React's modern rendering engine introduced in React 16.

---

# Why Fiber Was Created?

Old React:

```text
Synchronous Rendering
```

---

Problem:

```text
Large Updates Block UI
```

---

Example:

```text
10000 Components
```

---

Browser freezes.

---

Need:

```text
Interruptible Rendering
```

---

# Fiber Solution

React breaks work into:

```text
Small Units
```

---

Visualization:

```text
Task
 ↓

Small Piece
 ↓

Small Piece
 ↓

Small Piece
```

---

Browser remains responsive.

---

# Fiber Benefits

✔ Better performance

✔ Concurrent rendering

✔ Prioritization

✔ Suspense support

✔ Interruptible rendering

---

# Fiber Architecture

Each component becomes:

```text
Fiber Node
```

---

Structure:

```text
Fiber Tree
```

---

Instead of:

```text
Simple Component Tree
```

---

# Interview Question

### Why Fiber?

```text
To enable incremental rendering and prioritize updates.
```

---

# Controlled vs Uncontrolled Components

---

# Controlled Component

React controls input state.

---

Example

```jsx
const [name, setName] =
useState("");
```

---

```jsx
<input
 value={name}
 onChange={(e)=>
    setName(e.target.value)}
/>
```

---

Source of truth:

```text
React State
```

---

# Uncontrolled Component

DOM controls input.

---

Example

```jsx
const inputRef = useRef();
```

---

```jsx
<input ref={inputRef}/>
```

---

Read value:

```javascript
inputRef.current.value
```

---

Source of truth:

```text
DOM
```

---

# Comparison

| Controlled | Uncontrolled |
|------------|-------------|
| React Controls | DOM Controls |
| Predictable | Less Predictable |
| Validation Easy | Validation Hard |
| Recommended | Limited Use Cases |

---

# Props vs State

---

# Props

Data passed from parent.

---

Example

```jsx
<User name="John" />
```

---

Inside:

```jsx
props.name
```

---

Characteristics:

```text
Read Only

Immutable

Parent Controlled
```

---

# State

Managed by component itself.

---

Example

```jsx
const [count,setCount]
=
useState(0);
```

---

Characteristics:

```text
Mutable

Component Controlled

Causes Re-render
```

---

# Comparison

| Props | State |
|---------|---------|
| External | Internal |
| Read Only | Mutable |
| Parent Owned | Component Owned |

---

# useMemo vs useCallback

---

# useMemo

Caches:

```text
Value
```

---

Example

```jsx
const total = useMemo(
 () => calculate(),
 []
);
```

---

Returns:

```text
Computed Result
```

---

# useCallback

Caches:

```text
Function
```

---

Example

```jsx
const handleClick =
useCallback(
 () => {},
 []
);
```

---

Returns:

```text
Function Reference
```

---

# Comparison

| useMemo | useCallback |
|----------|------------|
| Memoize Value | Memoize Function |
| Returns Data | Returns Function |

---

# Memory Trick

```text
useMemo
    ↓
Memoize Result

useCallback
    ↓
Memoize Function
```

---

# Context API vs Redux

---

# Context API

Built into React.

---

Purpose:

```text
Avoid Prop Drilling
```

---

Example:

```jsx
<UserContext.Provider>
```

---

Best for:

```text
Theme

Language

Authentication
```

---

# Redux

Dedicated state management library.

---

Provides:

```text
Store

Reducers

Actions

Middleware
```

---

Best for:

```text
Large Applications
```

---

# Comparison

| Context API | Redux |
|-------------|---------|
| Built-in | External Library |
| Simple | Powerful |
| Small State | Large State |
| Basic Updates | Advanced Features |

---

# React.memo

> Prevents unnecessary component re-renders.

---

# Problem

Parent re-renders.

---

Child also re-renders.

---

Even if props unchanged.

---

# Solution

```jsx
React.memo(Component)
```

---

Example

```jsx
const User =
React.memo(
 function User() {
   return <h1>User</h1>;
 }
);
```

---

Now child re-renders only when:

```text
Props Change
```

---

# HOC vs Hooks

---

# HOC

Higher Order Component.

---

Example

```jsx
withAuth(Component)
```

---

Returns:

```jsx
Enhanced Component
```

---

# Hooks

Example:

```jsx
useAuth()
```

---

Returns:

```text
Reusable Logic
```

---

# Comparison

| HOC | Hooks |
|------|--------|
| Wrapper Component | Function |
| Nested Trees | Cleaner |
| Older Approach | Modern Approach |

---

# Functional vs Class Components

---

# Functional Component

```jsx
function User() {

 return <h1>Hello</h1>;

}
```

---

Uses Hooks.

---

Recommended.

---

# Class Component

```jsx
class User
extends Component {

 render() {

  return <h1>Hello</h1>;

 }

}
```

---

Uses lifecycle methods.

---

Older approach.

---

# Comparison

| Functional | Class |
|------------|--------|
| Hooks | Lifecycle Methods |
| Simpler | Verbose |
| Preferred | Legacy |

---

# Key Prop Importance

---

# Why Keys?

React identifies list items using keys.

---

Example

```jsx
users.map(user =>
(
 <li key={user.id}>
   {user.name}
 </li>
))
```

---

Without keys:

```text
Poor Reconciliation
```

---

Benefits:

✔ Better performance

✔ Correct updates

✔ Stable identity

---

# Lazy Loading

---

Definition:

```text
Load Component
Only When Needed
```

---

Example

```jsx
const Dashboard =
React.lazy(
 () => import("./Dashboard")
);
```

---

Benefits:

✔ Smaller Initial Bundle

✔ Faster First Load

---

# Code Splitting

---

Definition:

```text
Split Bundle
Into Multiple Files
```

---

Without:

```text
One Huge Bundle
```

---

With:

```text
Home Bundle

Admin Bundle

Dashboard Bundle
```

---

# Suspense

```jsx
<Suspense
 fallback={<Loader/>}
>
 <Dashboard/>
</Suspense>
```

---

# Error Boundaries

---

Definition:

> React components that catch rendering errors in child components.

---

Example

```jsx
class ErrorBoundary
extends Component
```

---

Catches:

```text
Render Errors

Lifecycle Errors
```

---

Does NOT catch:

```text
Async Errors

Event Handler Errors
```

---

# Custom Hooks

---

Definition:

```text
Reusable Hook Logic
```

---

Example

```jsx
function useFetch() {

}
```

---

Benefits:

✔ Reusability

✔ Cleaner Components

✔ Separation of Concerns

---

# Redux Toolkit

---

Modern Redux solution.

---

Solves:

```text
Boilerplate Code
```

---

Provides:

```text
configureStore

createSlice

createAsyncThunk

RTK Query
```

---

Most companies use:

```text
Redux Toolkit
```

instead of traditional Redux.

---

# React Rendering Lifecycle

---

# Initial Render

```text
Component Created
      ↓

Render
      ↓

Commit
      ↓

Paint
```

---

# Update Cycle

```text
State Change
      ↓

Render Phase
      ↓

Diffing
      ↓

Commit Phase
      ↓

DOM Update
```

---

# Unmount

```text
Component Removed
```

---

Cleanup runs.

---

Example

```jsx
return () => {

 clearInterval(id);

}
```

---

# React Performance Optimization

---

# Most Important Techniques

---

## React.memo

Prevent unnecessary renders.

---

## useMemo

Cache expensive values.

---

## useCallback

Cache functions.

---

## Lazy Loading

Load components on demand.

---

## Code Splitting

Reduce bundle size.

---

## Virtualization

Render only visible items.

---

Libraries:

```text
react-window

react-virtualized
```

---

## Debouncing

Delay execution.

---

Example:

```text
Search Input
```

---

User types:

```text
A
AB
ABC
```

---

Only final API call executes.

---

## Throttling

Limit execution frequency.

---

Example:

```text
Scroll Event
```

---

Run once every:

```text
500ms
```

---

# Interview Questions

---

### Q1. What is Virtual DOM?

```text
JavaScript Representation
Of Real DOM
```

---

### Q2. What is Reconciliation?

```text
Comparing Old And New
Virtual DOM Trees
```

---

### Q3. What is Diffing?

```text
Algorithm Used During
Reconciliation
```

---

### Q4. Why React Fiber?

```text
Incremental Rendering
And Better Performance
```

---

### Q5. Props vs State?

```text
Props = External

State = Internal
```

---

### Q6. useMemo vs useCallback?

```text
useMemo
     ↓
Value

useCallback
     ↓
Function
```

---

### Q7. Context API vs Redux?

```text
Context
     ↓
Simple Global State

Redux
     ↓
Complex Global State
```

---

### Q8. Why Keys?

```text
Help React Identify
List Items
```

---

### Q9. What is React.memo?

```text
Prevents Unnecessary
Re-renders
```

---

### Q10. What is Lazy Loading?

```text
Load Components
Only When Needed
```

---

# Ultimate Memory Trick

```text
Virtual DOM
      ↓
Cheap Copy

Reconciliation
      ↓
Find Changes

Diffing
      ↓
Compare Trees

Fiber
      ↓
Incremental Rendering

Props
      ↓
Parent Data

State
      ↓
Component Data

useMemo
      ↓
Memoize Value

useCallback
      ↓
Memoize Function

Context
      ↓
Simple Global State

Redux
      ↓
Advanced Global State

React.memo
      ↓
Prevent Re-render

Lazy Loading
      ↓
Load Later

Code Splitting
      ↓
Split Bundle

Error Boundary
      ↓
Catch UI Errors

Custom Hooks
      ↓
Reuse Logic

RTK
      ↓
Modern Redux

Performance
      ↓
Memoize + Split + Optimize
```

---

# Quick Revision

```text
Most Important React Interview Topics
-------------------------------------

Virtual DOM
 ↓
 JS Copy Of DOM

Reconciliation
 ↓
 Find Changes

Diffing
 ↓
 O(n) Comparison

Fiber
 ↓
 Incremental Rendering

Props
 ↓
 Parent Data

State
 ↓
 Component Data

useMemo
 ↓
 Cache Values

useCallback
 ↓
 Cache Functions

Context API
 ↓
 Small Global State

Redux
 ↓
 Large Global State

React.memo
 ↓
 Skip Re-renders

Keys
 ↓
 Identify List Items

Lazy Loading
 ↓
 Load On Demand

Code Splitting
 ↓
 Smaller Bundles

Error Boundaries
 ↓
 Catch UI Errors

Custom Hooks
 ↓
 Reusable Logic

Redux Toolkit
 ↓
 Modern Redux

Performance Optimization
 ↓
 Faster React Apps
```

---