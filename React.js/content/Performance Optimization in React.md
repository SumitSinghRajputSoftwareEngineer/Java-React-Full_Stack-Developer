# Performance Optimization in React

> **Goal:** Master React Performance Optimization completely — understand why React applications become slow, how React rendering works internally, React.memo, useMemo, useCallback, Lazy Loading, Code Splitting, Suspense, Debouncing, Throttling, Bundle Optimization, Memoization, Preventing Unnecessary Re-renders, Real-World Examples, Performance Bottlenecks, and Interview Questions.

---

# 1. Why Performance Optimization Matters?

A small React application:

```text
Login Page

Dashboard

Profile Page
```

---

May work perfectly.

---

But large applications:

```text
Amazon

Netflix

Facebook

LinkedIn
```

---

Contain:

```text
Thousands Of Components

Large Data Sets

Multiple API Calls

Complex UI Updates
```

---

Without optimization:

```text
Slow UI

Laggy Inputs

Unnecessary Re-renders

High Memory Usage
```

---

Need:

```text
Performance Optimization
```

---

# What Causes Slow React Applications?

Common reasons:

```text
Frequent Re-renders

Large Component Trees

Heavy Calculations

Large Bundles

Too Many API Calls

Unoptimized Lists
```

---

Example:

```jsx
function App() {

 console.log("Rendered");

 return <Child />;

}
```

---

Every state update:

```text
Re-renders Entire Tree
```

---

Can become expensive.

---

# React Rendering Refresher

Before optimization, understand rendering.

---

# 2. React Rendering Process

Suppose:

```jsx
setCount(count + 1);
```

---

Flow:

```text
State Changes
      ↓

Component Re-renders
      ↓

Virtual DOM Created
      ↓

Diffing Algorithm
      ↓

Real DOM Updated
```

---

Every state update causes:

```text
Render Cycle
```

---

Optimization means:

```text
Reduce Expensive Work
```

---

# React.memo

---

# 3. What is React.memo?

React.memo is:

```text
Component Memoization
```

---

Purpose:

```text
Prevent Unnecessary Re-renders
```

---

# Problem

Parent re-renders:

```jsx
function Parent() {

 const [count,setCount]

 =
 useState(0);

 return (

  <>
   <Child />
  </>

 );

}
```

---

Child also re-renders.

---

Even if:

```text
Child Props Never Changed
```

---

Wasteful.

---

# Example

```jsx
function Child() {

 console.log("Child Render");

 return <h1>Child</h1>;

}
```

---

Every parent update:

```text
Child Render
```

---

# Solution

```jsx
const Child = React.memo(
 function Child() {

  return <h1>Child</h1>;

 }
);
```

---

Now:

```text
Props Same
      ↓

Skip Re-render
```

---

# Internal Working

React compares:

```text
Previous Props

Current Props
```

---

If equal:

```text
Skip Render
```

---

Visualization:

```text
Props Changed?
      ↓

    YES
      ↓

 Re-render

    NO
      ↓

 Skip Render
```

---

# Example

```jsx
const User = React.memo(

 ({name}) => {

  console.log("Render");

  return <h1>{name}</h1>;

 }

);
```

---

If:

```text
name = "John"
```

still remains:

```text
name = "John"
```

---

Render skipped.

---

# React.memo Limitations

Works only for:

```text
Props Comparison
```

---

Does NOT help if:

```jsx
<Child data={{name:"John"}} />
```

---

Because:

```text
New Object Created
```

every render.

---

Will re-render.

---

# useMemo

---

# 4. What is useMemo?

Purpose:

```text
Memoize Computed Values
```

---

Avoid expensive recalculations.

---

# Problem

```jsx
const total =

 products.reduce(...)
```

---

Runs:

```text
Every Render
```

---

Even if products never changed.

---

Wasteful.

---

# Solution

```jsx
const total =

 useMemo(() => {

  return products.reduce(
   ...
  );

 }, [products]);
```

---

Now:

```text
Products Changed?
      ↓

YES → Recalculate

NO  → Reuse Cache
```

---

# Internal Working

Without useMemo:

```text
Render
 ↓

Calculation
```

---

Every render.

---

With useMemo:

```text
Render
 ↓

Dependency Changed?
      ↓

YES → Calculate

NO → Use Cached Value
```

---

# Example

```jsx
const expensiveValue =

 useMemo(() => {

  console.log("Running");

  return calculateHugeData();

 }, []);
```

---

Runs:

```text
Only Once
```

---

# Real World Example

Filtering products:

```jsx
const filteredProducts =

 useMemo(() => {

  return products.filter(

   product =>

   product.price > 1000

  );

 }, [products]);
```

---

Avoids repeated filtering.

---

# When NOT To Use useMemo

Wrong:

```jsx
const x = useMemo(

 ()=> count + 1,

 [count]

);
```

---

Simple calculation.

---

Overhead may exceed benefit.

---

Use only for:

```text
Expensive Computations
```

---

# useCallback

---

# 5. What is useCallback?

Purpose:

```text
Memoize Functions
```

---

# Problem

Every render:

```jsx
const handleClick = () => {

 console.log("Clicked");

}
```

---

Creates:

```text
New Function Instance
```

---

Each render.

---

Causes:

```text
React.memo Components
To Re-render
```

---

# Solution

```jsx
const handleClick =

 useCallback(() => {

  console.log("Clicked");

 }, []);
```

---

Function reused.

---

# Internal Working

Without useCallback:

```text
Render
 ↓

New Function
```

---

With useCallback:

```text
Render
 ↓

Dependency Changed?

 NO
 ↓

Reuse Function
```

---

# Example

```jsx
const Child =
React.memo(({onClick}) => {

 return (
  <button
   onClick={onClick}
  >
   Click
  </button>
 );

});
```

---

Parent:

```jsx
const handleClick =

 useCallback(() => {

  console.log("Click");

 }, []);
```

---

Now Child:

```text
Does Not Re-render
```

---

# useMemo vs useCallback

---

# 6. Difference

useMemo:

```text
Caches Values
```

---

Example:

```jsx
const total =
useMemo(...)
```

---

useCallback:

```text
Caches Functions
```

---

Example:

```jsx
const handleClick =
useCallback(...)
```

---

Memory Trick:

```text
useMemo
 ↓
 Memoize Value

useCallback
 ↓
 Memoize Callback Function
```

---

# Lazy Loading

---

# 7. What is Lazy Loading?

Load code:

```text
Only When Needed
```

---

Instead of:

```text
Load Everything Initially
```

---

# Problem

Application:

```text
Dashboard

Admin

Profile

Reports
```

---

All code loaded initially.

---

Huge bundle.

---

Slow startup.

---

# Solution

```text
Lazy Loading
```

---

# Example

```jsx
const AdminPage =

 React.lazy(() =>

  import("./AdminPage")

 );
```

---

Admin code loaded:

```text
Only When Visited
```

---

# Visualization

Without Lazy Loading:

```text
App
 ↓

Load Everything
```

---

With Lazy Loading:

```text
App
 ↓

Load Needed Pages Only
```

---

# Code Splitting

---

# 8. What is Code Splitting?

Breaking large bundle into:

```text
Smaller Chunks
```

---

Example:

Without:

```text
bundle.js

5 MB
```

---

With splitting:

```text
home.js

admin.js

profile.js
```

---

Smaller downloads.

---

Faster startup.

---

# React Supports Automatically

Using:

```jsx
React.lazy()
```

---

# Suspense

---

# 9. What is Suspense?

Displays fallback UI while lazy component loads.

---

Example:

```jsx
<Suspense

 fallback={<h1>

 Loading...

 </h1>}

>

 <AdminPage />

</Suspense>
```

---

Flow:

```text
User Opens Page
       ↓

Chunk Downloading
       ↓

Loading UI
       ↓

Component Ready
       ↓

Render Component
```

---

# Real Example

```jsx
const Dashboard =

 React.lazy(() =>

  import("./Dashboard")

 );
```

---

```jsx
<Suspense

 fallback={<Spinner />}

>

 <Dashboard />

</Suspense>
```

---

# Debouncing

---

# 10. What is Debouncing?

Wait before executing function.

---

Useful for:

```text
Search Boxes
```

---

Problem:

User types:

```text
R
Re
Rea
Reac
React
```

---

Without debounce:

```text
5 API Calls
```

---

Wasteful.

---

# Solution

Debounce.

---

User stops typing:

```text
500ms
```

---

Then API called.

---

# Visualization

```text
Typing...
Typing...
Typing...
Typing...
Stop
 ↓

API Call
```

---

# Example

```jsx
const debouncedSearch =

 debounce(

  (value) => {

   fetchResults(value);

  },

  500

 );
```

---

# Benefits

```text
Less API Calls

Better Performance

Lower Server Load
```

---

# Throttling

---

# 11. What is Throttling?

Execute function:

```text
At Fixed Intervals
```

---

Useful for:

```text
Scroll Events

Resize Events

Mouse Move Events
```

---

Without throttling:

```text
Thousands Of Calls
```

---

# Example

```text
Scroll Event
```

fires continuously.

---

Throttle:

```text
Once Every 200ms
```

---

# Visualization

Without:

```text
Call Call Call Call Call
```

---

With Throttle:

```text
Call
     ↓

200ms

Call
```

---

# Difference

Debounce:

```text
Execute After User Stops
```

---

Throttle:

```text
Execute Every X Milliseconds
```

---

# Bundle Optimization

---

# 12. What is Bundle Optimization?

Reduce JavaScript size.

---

Goal:

```text
Faster Loading
```

---

# Techniques

```text
Tree Shaking

Code Splitting

Lazy Loading

Compression
```

---

# Tree Shaking

Removes:

```text
Unused Code
```

---

Example:

```jsx
import {

 Button

}
from "library";
```

---

Unused components removed.

---

Smaller bundle.

---

# Memoization

---

# 13. What is Memoization?

Caching results.

---

Example:

```text
Expensive Calculation
```

---

Store result.

---

Reuse later.

---

Without:

```text
Calculate Every Time
```

---

With:

```text
Calculate Once
Reuse Later
```

---

# Example

```jsx
const value =

 useMemo(() => {

  return heavyCalculation();

 }, []);
```

---

Memoized result.

---

# Preventing Unnecessary Re-renders

---

# 14. Why Re-renders Matter?

React rendering is not free.

---

Every render:

```text
Component Function Runs

Virtual DOM Created

Diffing Happens
```

---

Too many renders:

```text
Performance Issues
```

---

# Common Causes

---

## Inline Functions

Wrong:

```jsx
<button

 onClick={() =>
  doSomething()
 }

>
```

---

Creates new function.

---

Use:

```jsx
const handleClick =
useCallback(...);
```

---

# Inline Objects

Wrong:

```jsx
<Child

 style={{

  color:"red"

 }}

/>
```

---

New object each render.

---

Use:

```jsx
const style =

 useMemo(() => ({

  color:"red"

 }), []);
```

---

# Large Parent Components

State change:

```text
Parent Re-renders
```

---

All children re-render.

---

Use:

```text
React.memo
```

---

# Optimization Strategy

---

# 15. Real World Performance Checklist

Before optimizing:

```text
Measure Performance
```

---

React DevTools Profiler:

```text
Find Slow Components
```

---

Then optimize.

---

Checklist:

```text
✔ React.memo

✔ useMemo

✔ useCallback

✔ Lazy Loading

✔ Code Splitting

✔ Suspense

✔ Debouncing

✔ Throttling

✔ Tree Shaking

✔ Memoization
```

---

# Common Mistakes

---

# 16. Overusing useMemo

Wrong:

```jsx
const x =

 useMemo(
  ()=>count+1,
  [count]
 );
```

---

Not expensive.

---

Optimization becomes overhead.

---

# 17. Overusing useCallback

Wrong:

```jsx
useCallback(

 ()=>console.log("Hi"),

 []

);
```

---

No benefit.

---

Use only when needed.

---

# 18. Using React.memo Everywhere

Not every component needs memoization.

---

Small components:

```text
May Become Slower
```

---

Because React must compare props.

---

# Interview Questions

---

### Q1. What is React.memo?

```text
Prevents Unnecessary
Component Re-renders
```

---

### Q2. useMemo vs useCallback?

useMemo:

```text
Caches Value
```

---

useCallback:

```text
Caches Function
```

---

### Q3. What is Lazy Loading?

```text
Load Code Only
When Needed
```

---

### Q4. What is Code Splitting?

```text
Split Bundle Into Chunks
```

---

### Q5. What is Suspense?

```text
Displays Fallback UI
While Loading
```

---

### Q6. Debounce vs Throttle?

Debounce:

```text
Wait Until User Stops
```

---

Throttle:

```text
Run At Fixed Interval
```

---

### Q7. What is Memoization?

```text
Cache Computation Results
```

---

### Q8. Why Prevent Re-renders?

```text
Improve Performance
```

---

### Q9. What causes unnecessary re-renders?

```text
New Objects

New Functions

Parent Updates
```

---

### Q10. What tool identifies rendering issues?

```text
React DevTools Profiler
```

---

# Memory Tricks

### React.memo

```text
Memoize Component
```

---

### useMemo

```text
Memoize Value
```

---

### useCallback

```text
Memoize Function
```

---

### Lazy Loading

```text
Load Later
```

---

### Code Splitting

```text
Split Bundle
```

---

### Suspense

```text
Loading UI
```

---

### Debounce

```text
Wait Then Execute
```

---

### Throttle

```text
Execute Every X Time
```

---

### Bundle Optimization

```text
Reduce Bundle Size
```

---

### Memoization

```text
Cache Results
```

---

# Complete Optimization Flow

```text
User Action
      ↓

State Update
      ↓

React Render
      ↓

Optimization Layer

React.memo
useMemo
useCallback

      ↓

Less Work
      ↓

Better Performance
```

---

# Quick Revision

```text
Performance Optimization
------------------------

React.memo
 ↓
 Skip Re-renders

useMemo
 ↓
 Cache Values

useCallback
 ↓
 Cache Functions

Lazy Loading
 ↓
 Load When Needed

Code Splitting
 ↓
 Smaller Bundles

Suspense
 ↓
 Loading UI

Debounce
 ↓
 Wait Then Execute

Throttle
 ↓
 Execute At Interval

Bundle Optimization
 ↓
 Faster Loading

Memoization
 ↓
 Cache Results

Prevent Re-renders
 ↓
 Improve Performance

Remember:

React.memo
 ↓ Component

useMemo
 ↓ Value

useCallback
 ↓ Function
```

---