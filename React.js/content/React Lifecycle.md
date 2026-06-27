# React Lifecycle

> **Goal:** Master React Lifecycle completely — understand what a component lifecycle is, how React creates, updates, and destroys components, Class Component Lifecycle Methods, Functional Component Lifecycle using Hooks, Mounting, Updating, Unmounting Phases, Cleanup Functions, Internal React Rendering Flow, Real-World Examples, Best Practices, Common Mistakes, and Interview Questions.

---

# 1. What is React Lifecycle?

Every React component goes through a lifecycle.

Just like humans:

```text
Born
 ↓

Live
 ↓

Grow
 ↓

Die
```

---

React Components:

```text
Created
 ↓

Rendered
 ↓

Updated
 ↓

Removed
```

---

This entire process is called:

```text
Component Lifecycle
```

---

# Why Lifecycle Matters?

Many operations depend on lifecycle stages.

Examples:

```text
API Calls

Subscriptions

Timers

WebSocket Connections

Event Listeners

Cleanup Operations
```

---

Without lifecycle knowledge:

```text
Memory Leaks

Duplicate API Calls

Performance Problems

Unexpected Bugs
```

---

# Real World Example

Suppose:

```text
User Opens Dashboard
```

---

Need:

```text
Load User Data
```

---

When dashboard closes:

```text
Remove Event Listeners

Close WebSocket

Clear Timers
```

---

Lifecycle helps manage this.

---

# React Lifecycle Phases

---

# 2. Three Main Lifecycle Phases

React lifecycle is divided into:

```text
1. Mounting

2. Updating

3. Unmounting
```

---

Visualization:

```text
Component Created
        ↓

     Mounting
        ↓

     Updating
        ↓

    Unmounting
```

---

# Phase 1: Mounting

Component appears for first time.

---

Example:

```jsx
<UserProfile />
```

---

Rendered first time.

---

Mounting.

---

# Phase 2: Updating

Occurs when:

```text
Props Change

State Changes

Parent Re-renders
```

---

Example:

```jsx
setCount(count + 1);
```

---

Component updates.

---

# Phase 3: Unmounting

Component removed from DOM.

---

Example:

```jsx
{
 show && <Modal />
}
```

---

When:

```jsx
show = false
```

---

Modal removed.

---

Unmounting occurs.

---

# Class Component Lifecycle

---

# 3. Lifecycle Before Hooks

Before React Hooks:

```text
Class Components
```

were used heavily.

---

Lifecycle methods handled:

```text
Mounting

Updating

Unmounting
```

---

Most important methods:

```text
componentDidMount()

componentDidUpdate()

componentWillUnmount()
```

---

# Class Lifecycle Flow

```text
Constructor
      ↓

Render
      ↓

componentDidMount
      ↓

State Update
      ↓

Render Again
      ↓

componentDidUpdate
      ↓

Component Removed
      ↓

componentWillUnmount
```

---

# componentDidMount()

---

# 4. What is componentDidMount()?

Runs:

```text
After Component Mounts
```

---

Only:

```text
Once
```

---

# Visualization

```text
Component Created
      ↓

Rendered
      ↓

componentDidMount()
```

---

# Syntax

```jsx
componentDidMount(){

}
```

---

# Example

```jsx
class User extends React.Component {

 componentDidMount(){

  console.log(
   "Mounted"
  );

 }

 render(){

  return <h1>User</h1>;

 }

}
```

---

Output:

```text
Mounted
```

---

Only once.

---

# Common Uses

---

## API Calls

```jsx
componentDidMount(){

 fetchUsers();

}
```

---

## WebSocket Connections

```jsx
componentDidMount(){

 connectSocket();

}
```

---

## Event Listeners

```jsx
componentDidMount(){

 window.addEventListener(
  "resize",
  this.handleResize
 );

}
```

---

# Real World Example

Dashboard opens:

```text
Load User Data
```

---

Example:

```jsx
componentDidMount(){

 axios.get("/users");

}
```

---

Perfect use case.

---

# componentDidUpdate()

---

# 5. What is componentDidUpdate()?

Runs:

```text
After Every Update
```

---

Updates occur when:

```text
Props Change

State Changes
```

---

# Visualization

```text
State Changes
      ↓

Render
      ↓

componentDidUpdate()
```

---

# Syntax

```jsx
componentDidUpdate(
 prevProps,
 prevState
){

}
```

---

# Example

```jsx
componentDidUpdate(){

 console.log(
  "Updated"
 );

}
```

---

Runs after update.

---

# Prevent Infinite Loops

Wrong:

```jsx
componentDidUpdate(){

 this.setState({

  count:1

 });

}
```

---

Flow:

```text
Update
 ↓

setState
 ↓

Update
 ↓

setState
 ↓

Infinite Loop
```

---

# Correct Way

```jsx
componentDidUpdate(

 prevProps

){

 if(

  prevProps.id

  !==

  this.props.id

 ){

  fetchUser();

 }

}
```

---

Runs only when needed.

---

# Common Uses

---

## Refetch Data

```jsx
componentDidUpdate(
 prevProps
){

 if(

  prevProps.userId
  !==

  this.props.userId

 ){

  loadUser();

 }

}
```

---

## Track Changes

```jsx
componentDidUpdate(){

 console.log(
  "Data Changed"
 );

}
```

---

# componentWillUnmount()

---

# 6. What is componentWillUnmount()?

Runs:

```text
Before Component Is Removed
```

---

Purpose:

```text
Cleanup Resources
```

---

# Visualization

```text
Component Exists
      ↓

Removed
      ↓

componentWillUnmount()
```

---

# Syntax

```jsx
componentWillUnmount(){

}
```

---

# Example

```jsx
componentWillUnmount(){

 console.log(
  "Goodbye"
 );

}
```

---

Before component disappears.

---

# Common Uses

---

## Remove Event Listeners

```jsx
componentWillUnmount(){

 window.removeEventListener(
  "resize",
  this.handleResize
 );

}
```

---

## Clear Timers

```jsx
componentWillUnmount(){

 clearInterval(
  this.timer
 );

}
```

---

## Close WebSocket

```jsx
componentWillUnmount(){

 socket.close();

}
```

---

# Real World Example

Chat application:

```text
Open Chat
```

---

WebSocket connects.

---

User leaves:

```text
Close WebSocket
```

---

Otherwise:

```text
Memory Leak
```

---

# Complete Class Lifecycle Example

---

# 7. Example

```jsx
class User extends React.Component {

 componentDidMount(){

  console.log(
   "Mounted"
  );

 }

 componentDidUpdate(){

  console.log(
   "Updated"
  );

 }

 componentWillUnmount(){

  console.log(
   "Unmounted"
  );

 }

 render(){

  return <h1>User</h1>;

 }

}
```

---

Possible output:

```text
Mounted

Updated

Updated

Unmounted
```

---

# Functional Component Lifecycle

---

# 8. Lifecycle After Hooks

Modern React uses:

```text
Functional Components
```

---

Lifecycle handled using:

```jsx
useEffect()
```

---

One hook replaces:

```text
componentDidMount

componentDidUpdate

componentWillUnmount
```

---

# Lifecycle Mapping

| Class Component | Functional Component |
|---------------|---------------|
| componentDidMount | useEffect(() => {}, []) |
| componentDidUpdate | useEffect(() => {}, [dependency]) |
| componentWillUnmount | Cleanup Function |

---

# useEffect Equivalent

---

# 9. Mounting Equivalent

Class:

```jsx
componentDidMount(){

 fetchUsers();

}
```

---

Functional:

```jsx
useEffect(() => {

 fetchUsers();

}, []);
```

---

Empty dependency array:

```jsx
[]
```

means:

```text
Run Once
```

---

Equivalent to:

```text
componentDidMount()
```

---

# Internal Flow

```text
Component Rendered
      ↓

useEffect Runs
```

---

Only once.

---

# Updating Equivalent

---

# 10. componentDidUpdate Equivalent

Class:

```jsx
componentDidUpdate(){

}
```

---

Functional:

```jsx
useEffect(() => {

 console.log(
  "Updated"
 );

}, [count]);
```

---

Runs when:

```text
count changes
```

---

Flow:

```text
Count Changes
      ↓

Render
      ↓

Effect Runs
```

---

# Example

```jsx
useEffect(() => {

 fetchUser();

}, [userId]);
```

---

Whenever:

```text
userId changes
```

---

Fetch again.

---

# Multiple Dependency Example

```jsx
useEffect(() => {

 console.log(
  "Run"
 );

}, [

 count,

 user

]);
```

---

Runs when:

```text
count changes

OR

user changes
```

---

# Cleanup Functions

---

# 11. What is Cleanup Function?

Used to:

```text
Clean Resources
```

---

Equivalent to:

```text
componentWillUnmount()
```

---

# Syntax

```jsx
useEffect(() => {

 return () => {

 };

}, []);
```

---

Returned function:

```text
Cleanup Function
```

---

# Example

```jsx
useEffect(() => {

 return () => {

  console.log(
   "Cleanup"
  );

 };

}, []);
```

---

Runs before unmount.

---

# Visualization

```text
Mount
 ↓

Effect Runs
 ↓

Component Lives
 ↓

Unmount
 ↓

Cleanup Runs
```

---

# Real World Example

Event Listener

---

Add:

```jsx
useEffect(() => {

 window.addEventListener(
  "resize",
  handleResize
 );

 return () => {

  window.removeEventListener(
   "resize",
   handleResize
  );

 };

}, []);
```

---

Prevents memory leaks.

---

# Timer Example

```jsx
useEffect(() => {

 const timer =

 setInterval(() => {

  console.log(
   "Running"
  );

 },1000);

 return () => {

  clearInterval(
   timer
  );

 };

}, []);
```

---

Without cleanup:

```text
Timer Continues Forever
```

---

Bad.

---

# WebSocket Example

```jsx
useEffect(() => {

 const socket =

 new WebSocket(
  "url"
 );

 return () => {

  socket.close();

 };

}, []);
```

---

Proper cleanup.

---

# Effect Lifecycle Internally

---

# 12. Complete useEffect Lifecycle

Initial Render:

```text
Render
 ↓

Effect Runs
```

---

Dependency Changes:

```text
Render
 ↓

Previous Cleanup
 ↓

New Effect
```

---

Unmount:

```text
Cleanup
```

---

# Example

```jsx
useEffect(() => {

 console.log(
  "Effect"
 );

 return () => {

  console.log(
   "Cleanup"
  );

 };

}, [count]);
```

---

Count changes:

```text
Cleanup
 ↓

Effect
```

---

Important interview question.

---

# Class vs Functional Lifecycle

---

# 13. Comparison

| Class Lifecycle | Functional Equivalent |
|----------|----------|
| componentDidMount | useEffect(..., []) |
| componentDidUpdate | useEffect(..., [dep]) |
| componentWillUnmount | Cleanup Function |
| Multiple Lifecycle Methods | Single Hook |
| More Boilerplate | Less Boilerplate |

---

# Why Hooks Replaced Lifecycle Methods?

Hooks provide:

```text
Cleaner Code

Less Boilerplate

Reusable Logic

Custom Hooks
```

---

Example:

Class:

```text
100+ Lines
```

---

Functional:

```text
Much Smaller
```

---

# Common Mistakes

---

# 14. Missing Dependency Array

Wrong:

```jsx
useEffect(() => {

 fetchUsers();

});
```

---

Runs:

```text
Every Render
```

---

Potential API spam.

---

# Infinite Loop

Wrong:

```jsx
useEffect(() => {

 setCount(
  count + 1
 );

}, [count]);
```

---

Flow:

```text
Update
 ↓

Effect
 ↓

Update
 ↓

Effect
```

---

Infinite loop.

---

# Forgetting Cleanup

Wrong:

```jsx
useEffect(() => {

 setInterval(...);

}, []);
```

---

Timer never removed.

---

Memory leak.

---

# Best Practices

---

# 15. Lifecycle Best Practices

✔ Use useEffect for side effects

✔ Always cleanup subscriptions

✔ Cleanup timers

✔ Cleanup event listeners

✔ Cleanup WebSockets

✔ Keep effects focused

✔ Avoid unnecessary effects

---

# Interview Questions

---

### Q1. What are the three lifecycle phases?

```text
Mounting

Updating

Unmounting
```

---

### Q2. When does componentDidMount run?

```text
After First Render
```

---

### Q3. When does componentDidUpdate run?

```text
After State Or Props Change
```

---

### Q4. When does componentWillUnmount run?

```text
Before Component Removal
```

---

### Q5. What hook replaces lifecycle methods?

```text
useEffect()
```

---

### Q6. How do you mimic componentDidMount?

```jsx
useEffect(() => {

}, []);
```

---

### Q7. How do you mimic componentWillUnmount?

```jsx
return () => {};
```

inside useEffect.

---

### Q8. Why are cleanup functions important?

```text
Prevent Memory Leaks
```

---

### Q9. What happens before an effect re-runs?

```text
Previous Cleanup Executes
```

---

### Q10. Why did React move to Hooks?

```text
Cleaner

Reusable

Less Boilerplate
```

---

# Memory Tricks

### Mounting

```text
Component Appears
```

---

### Updating

```text
Component Changes
```

---

### Unmounting

```text
Component Removed
```

---

### componentDidMount

```text
First Time Setup
```

---

### componentDidUpdate

```text
React To Changes
```

---

### componentWillUnmount

```text
Cleanup Resources
```

---

### useEffect([])

```text
Mount
```

---

### useEffect([dep])

```text
Update
```

---

### Cleanup Function

```text
Unmount
```

---

# Complete Lifecycle Flow

```text
Component Created
        ↓

Render
        ↓

Mount Phase
        ↓

componentDidMount
OR
useEffect([])

        ↓

State/Props Change
        ↓

Render Again
        ↓

componentDidUpdate
OR
useEffect([dep])

        ↓

Cleanup Previous Effect

        ↓

Component Removed
        ↓

componentWillUnmount
OR
Cleanup Function
```

---

# Quick Revision

```text
React Lifecycle
---------------

Mounting
 ↓
 Component Created

Updating
 ↓
 State/Props Changed

Unmounting
 ↓
 Component Removed

Class Lifecycle:

componentDidMount
 ↓
 First Render

componentDidUpdate
 ↓
 After Updates

componentWillUnmount
 ↓
 Cleanup

Functional Lifecycle:

useEffect([])
 ↓
 Mount

useEffect([dep])
 ↓
 Update

return () => {}
 ↓
 Cleanup

Remember:

Mount
 ↓
 Setup

Update
 ↓
 React To Change

Unmount
 ↓
 Cleanup
```

---