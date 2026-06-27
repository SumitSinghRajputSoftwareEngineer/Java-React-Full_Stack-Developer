# useMemo Hook

> **Goal:** Understand useMemo from scratch, why React created it, Memoization, Expensive Calculations, Dependency Arrays, Re-render Optimization, Internal Working, Real-World Examples, Performance Benefits, Common Mistakes, Best Practices, Interview Questions, and React Internals.

---

# 1. What Problem Does useMemo Solve?

React components re-render frequently.

Example:

```jsx
function App() {

  const [count, setCount] =
  useState(0);

  return (
    <>
      <h1>{count}</h1>

      <button
        onClick={() =>
          setCount(count + 1)
        }
      >
        Increment
      </button>
    </>
  );
}
```

---

Every state update:

```text
Component Re-renders
```

---

Normally this is fine.

---

But imagine a component performing:

```text
Heavy Calculations

Large Data Processing

Sorting

Filtering

Searching

Mathematical Computations
```

---

Example:

```jsx
const result =
calculateHugeData();
```

---

Every re-render:

```text
calculateHugeData()
Runs Again
```

---

Even if data hasn't changed.

---

This causes:

```text
Performance Problems
```

---

Need:

```text
Reuse Previous Result
```

---

React provides:

```jsx
useMemo()
```

---

# 2. What is Memoization?

Before learning useMemo, understand:

```text
Memoization
```

---

Definition:

> Memoization is an optimization technique where previously computed results are stored and reused.

---

Example:

```text
2 + 3 = 5
```

---

Instead of recalculating:

```text
Store Result
```

---

Next time:

```text
Return Cached Value
```

---

Without recalculation.

---

# 3. Simple Real-Life Example

Suppose:

```text
You Solve

500 × 500
```

---

Result:

```text
250000
```

---

Friend asks again.

---

Options:

### Without Memoization

```text
Calculate Again
```

---

### With Memoization

```text
Already Know Answer

Use Cached Result
```

---

Faster.

---

# 4. What is useMemo?

Definition:

> useMemo is a React Hook that memoizes a calculated value and recomputes it only when dependencies change.

---

Simple Meaning:

```text
Remember Calculation Result
```

---

# 5. Syntax

```jsx
const value = useMemo(
  () => {

    return calculation;

  },
  [dependencies]
);
```

---

Structure:

```jsx
useMemo(

 callback,

 dependencies

);
```

---

Returns:

```text
Memoized Value
```

---

# 6. Parameters

### Callback

```jsx
() => {

 return result;

}
```

---

Contains:

```text
Expensive Calculation
```

---

### Dependency Array

```jsx
[]
```

---

Controls:

```text
When Recalculate
```

---

# 7. First Example

Without useMemo:

```jsx
function App() {

 const [count,
 setCount] =
 useState(0);

 const number = 5;

 const squared =
 number * number;

 return (
  <h1>{squared}</h1>
 );
}
```

---

Works.

---

But calculation runs:

```text
Every Render
```

---

Now with useMemo:

```jsx
const squared =
useMemo(() => {

 return number * number;

}, [number]);
```

---

Runs only when:

```text
number changes
```

---

# 8. Visual Flow

Without useMemo:

```text
Render
 ↓

Calculate
 ↓

Render
 ↓

Calculate
 ↓

Render
 ↓

Calculate
```

---

With useMemo:

```text
Render
 ↓

Calculate Once
 ↓

Reuse Result
 ↓

Reuse Result
```

---

# Expensive Calculation Example

---

# 9. Example Function

```jsx
function expensiveTask() {

 console.log(
  "Calculating..."
 );

 let total = 0;

 for(
  let i = 0;
  i < 100000000;
  i++
 ) {

  total += i;
 }

 return total;
}
```

---

Very expensive.

---

# 10. Without useMemo

```jsx
function App() {

 const [count,
 setCount] =
 useState(0);

 const total =
 expensiveTask();

 return (
  <>
   <h1>{count}</h1>

   <button
    onClick={() =>
    setCount(count+1)}
   >
    Click
   </button>
  </>
 );
}
```

---

Click button:

```text
Re-render
```

---

Result:

```text
expensiveTask()

Runs Again
```

---

Every click.

---

Bad performance.

---

# 11. With useMemo

```jsx
const total =
useMemo(() => {

 return expensiveTask();

}, []);
```

---

Now:

```text
Calculation Runs Once
```

---

Subsequent renders:

```text
Cached Result
```

---

Used.

---

Huge performance improvement.

---

# Dependency Array

---

# 12. Dependency Array Controls Recalculation

Example:

```jsx
useMemo(() => {

 return number * 2;

}, [number]);
```

---

React watches:

```text
number
```

---

If number changes:

```text
Recalculate
```

---

Otherwise:

```text
Use Cached Value
```

---

# 13. Example

```jsx
const [number,
setNumber] =
useState(5);
```

---

```jsx
const double =
useMemo(() => {

 console.log(
  "Calculating"
 );

 return number * 2;

}, [number]);
```

---

Change:

```text
number
```

---

Output:

```text
Calculating
```

---

Again.

---

Otherwise:

```text
No Calculation
```

---

# 14. Empty Dependency Array

```jsx
useMemo(() => {

 return expensiveTask();

}, []);
```

---

Runs:

```text
Only Once
```

---

At initial render.

---

# 15. Multiple Dependencies

```jsx
useMemo(() => {

 return price * quantity;

}, [

 price,

 quantity

]);
```

---

Recalculates when:

```text
price changes

OR

quantity changes
```

---

# useMemo vs Normal Variable

---

# 16. Normal Variable

```jsx
const total =
calculate();
```

---

Runs:

```text
Every Render
```

---

# 17. useMemo

```jsx
const total =
useMemo(
 () => calculate(),
 []
);
```

---

Runs:

```text
Only When Needed
```

---

# Real World Example

---

# 18. Product Filtering

Imagine:

```text
10000 Products
```

---

Filtering:

```jsx
products.filter(...)
```

---

Can be expensive.

---

# 19. Without useMemo

```jsx
const filteredProducts =
products.filter(
 p => p.price > 1000
);
```

---

Runs:

```text
Every Render
```

---

Even unrelated renders.

---

# 20. With useMemo

```jsx
const filteredProducts =
useMemo(() => {

 return products.filter(
  p => p.price > 1000
 );

}, [products]);
```

---

Runs only when:

```text
products change
```

---

# Search Example

---

# 21. Search Application

```jsx
const filteredUsers =
useMemo(() => {

 return users.filter(
  user =>
  user.name
  .includes(search)
 );

}, [

 users,

 search

]);
```

---

Efficient.

---

Only recalculates when:

```text
Users Change

OR

Search Changes
```

---

# Sorting Example

---

# 22. Sorting Large Lists

```jsx
const sortedProducts =
useMemo(() => {

 return [...products]
 .sort(
  (a,b) =>
  a.price - b.price
 );

}, [products]);
```

---

Useful for:

```text
E-commerce Sites

Dashboards

Reports
```

---

# useMemo vs useEffect

---

# 23. Common Interview Question

Many developers confuse them.

---

## useMemo

Returns:

```text
Value
```

---

Example:

```jsx
const total =
useMemo(...)
```

---

## useEffect

Performs:

```text
Side Effects
```

---

Example:

```jsx
useEffect(...)
```

---

# 24. Comparison

| Feature | useMemo | useEffect |
|----------|----------|----------|
| Returns Value | Yes | No |
| Optimizes Calculation | Yes | No |
| Side Effects | No | Yes |
| API Calls | No | Yes |

---

# useMemo vs useCallback

---

# 25. Another Common Question

---

### useMemo

Memoizes:

```text
Value
```

---

Example:

```jsx
const result =
useMemo(...)
```

---

### useCallback

Memoizes:

```text
Function
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
Memoize Function
```

---

# Internal Working

---

# 26. What Happens Internally?

When React executes:

```jsx
useMemo(
 callback,
 [number]
)
```

---

React stores:

```text
Previous Value

Previous Dependencies
```

---

Inside Fiber.

---

# 27. First Render

```text
Execute Callback
```

---

Store:

```text
Result

Dependencies
```

---

# 28. Next Render

React compares:

```text
Old Dependency

vs

New Dependency
```

---

If changed:

```text
Recalculate
```

---

If unchanged:

```text
Return Cached Result
```

---

# 29. Visualization

```text
Dependency Changed?
       ↓

      Yes
       ↓

 Recalculate

      No
       ↓

 Use Cached Value
```

---

# Common Mistakes

---

# 30. Using useMemo Everywhere

Bad.

---

Example:

```jsx
const name =
useMemo(() => {

 return "John";

}, []);
```

---

Useless.

---

String calculation:

```text
Cheap
```

---

No optimization needed.

---

# 31. Overusing useMemo

Wrong:

```text
Everything Wrapped
In useMemo
```

---

Actually:

```text
Can Reduce Performance
```

---

Because React must:

```text
Track Dependencies

Store Cache
```

---

# 32. Missing Dependencies

Wrong:

```jsx
useMemo(() => {

 return number * 2;

}, []);
```

---

If number changes:

```text
Stale Value
```

---

Bug.

---

Correct:

```jsx
[number]
```

---

# 33. Side Effects Inside useMemo

Wrong:

```jsx
useMemo(() => {

 fetch("/users");

}, []);
```

---

useMemo is for:

```text
Calculations
```

---

Use:

```text
useEffect
```

for side effects.

---

# Best Practices

---

# 34. Use Only For Expensive Work

Examples:

```text
Filtering

Sorting

Searching

Complex Calculations
```

---

# 35. Measure First

Don't optimize blindly.

---

Use React DevTools.

---

Identify:

```text
Actual Bottlenecks
```

---

# 36. Keep Dependencies Correct

Always include:

```text
Used Variables
```

---

Inside dependency array.

---

# 37. Avoid Premature Optimization

Simple calculation:

```jsx
2 + 2
```

---

No need:

```jsx
useMemo()
```

---

# Real World Enterprise Example

---

# 38. Dashboard Analytics

Suppose:

```text
50000 Transactions
```

---

Need:

```text
Revenue

Profit

Growth

Statistics
```

---

Calculations expensive.

---

Use:

```jsx
const analytics =
useMemo(() => {

 return generateReport(
  transactions
 );

}, [transactions]);
```

---

Only recalculates when:

```text
transactions change
```

---

Huge optimization.

---

# Interview Questions

---

### Q1. What is useMemo?

```text
Hook That Memoizes
Calculated Values
```

---

### Q2. Why useMemo?

```text
Avoid Expensive
Recalculations
```

---

### Q3. What does useMemo return?

```text
Memoized Value
```

---

### Q4. Does useMemo run every render?

```text
No
```

---

Depends on:

```text
Dependencies
```

---

### Q5. Difference Between useMemo and useEffect?

```text
useMemo
 ↓
Returns Value

useEffect
 ↓
Performs Side Effects
```

---

### Q6. Difference Between useMemo and useCallback?

```text
useMemo
 ↓
Value

useCallback
 ↓
Function
```

---

### Q7. Can API Calls Be Made In useMemo?

```text
No
```

---

Use:

```text
useEffect
```

---

### Q8. Is useMemo Always Beneficial?

```text
No
```

---

Only for expensive calculations.

---

### Q9. What Happens If Dependency Changes?

```text
Value Recomputed
```

---

### Q10. What Does React Store Internally?

```text
Cached Value

Dependencies
```

---

# Memory Tricks

### Memoization

```text
Remember Result
```

---

### useMemo

```text
Memoize Value
```

---

### Dependency Array

```text
Controls Recalculation
```

---

### Expensive Calculation

```text
Good Candidate
For useMemo
```

---

### useMemo

```text
Value
```

---

### useCallback

```text
Function
```

---

### Rule

```text
Expensive Calculation
 ↓

useMemo
```

---

# Quick Revision

```text
useMemo
-------

Purpose:

Optimize Expensive Calculations

Returns:

Memoized Value

Syntax:

const value =
useMemo(
 () => result,
 [dependencies]
);

Flow:

Render
 ↓

Dependency Changed?

 ↓         ↓

Yes       No

 ↓         ↓

Recompute  Use Cache

Use Cases:

✔ Filtering

✔ Sorting

✔ Searching

✔ Analytics

✔ Large Data Processing

Advantages:

✔ Better Performance

✔ Avoid Recalculation

✔ Efficient Rendering

Disadvantages:

✘ Extra Memory

✘ Dependency Management

✘ Overuse Can Hurt Performance

Remember:

useMemo
 ↓

Memoize Value

useCallback
 ↓

Memoize Function

useEffect
 ↓

Side Effects
```

---