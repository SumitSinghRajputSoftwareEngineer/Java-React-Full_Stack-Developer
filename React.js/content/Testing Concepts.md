# Testing Concepts in React

> **Goal:** Master React Testing Concepts completely — understand Component Testing, Hook Testing, Snapshot Testing, Mock Functions, API Mocking, internal working, real-world examples, interview questions, best practices, and when to use each testing technique.

---

# 1. Why Do We Need Different Testing Concepts?

A React application contains many things:

```text
Components

Hooks

API Calls

Business Logic

Utilities

State Management
```

---

Testing everything the same way is difficult.

---

Different testing techniques exist for different purposes.

---

Example:

```text
Button Component
```

Needs:

```text
Component Testing
```

---

Example:

```text
Custom Hook
```

Needs:

```text
Hook Testing
```

---

Example:

```text
Backend API
```

Needs:

```text
API Mocking
```

---

# Testing Overview

```text
React Application
       ↓

Component Testing

Hook Testing

Snapshot Testing

Mock Functions

API Mocking
```

---

Each solves a different problem.

---

# Component Testing

---

# 2. What is Component Testing?

Definition:

> Component Testing verifies that a React component renders and behaves correctly.

---

Focus:

```text
UI

Props

Events

State Changes

User Interaction
```

---

# Real World Example

Component:

```jsx
<LoginButton />
```

---

Expected:

```text
Button Visible
```

---

When clicked:

```text
Login Function Called
```

---

Component Testing verifies this.

---

# Example Component

```jsx
function Welcome() {

 return (

  <h1>
   Welcome User
  </h1>

 );

}
```

---

# Test

```jsx
import {

 render,
 screen

}
from
"@testing-library/react";
```

---

```jsx
test(

 "renders heading",

 () => {

  render(
   <Welcome />
  );

  expect(

   screen.getByText(
    "Welcome User"
   )

  )
  .toBeInTheDocument();

 }

);
```

---

Pass.

---

# Component Testing Flow

```text
Render Component
       ↓

Find Element
       ↓

Interact
       ↓

Verify Result
```

---

# Testing User Interactions

Component:

```jsx
<button>
 Increment
</button>
```

---

Test:

```jsx
await userEvent.click(
 button
);
```

---

Verify:

```text
Count Increased
```

---

# What Should Be Tested?

---

✔ Rendering

✔ Props

✔ User Interaction

✔ State Changes

✔ Conditional Rendering

✔ Form Submission

✔ Error Messages

---

# What Should NOT Be Tested?

---

❌ Internal State Variables

❌ React Internals

❌ Third-Party Libraries

---

# Example

Bad:

```javascript
expect(
 component.state.count
)
.toBe(1);
```

---

Good:

```javascript
expect(
 screen.getByText("1")
)
.toBeInTheDocument();
```

---

Test behavior.

---

# Hook Testing

---

# 3. What is Hook Testing?

Definition:

> Hook Testing verifies that custom hooks work correctly.

---

Hooks contain:

```text
State Logic

Effects

Data Fetching

Reusable Logic
```

---

Need testing.

---

# Example Hook

```javascript
function useCounter() {

 const [
  count,
  setCount
 ]
 =
 useState(0);

 const increment =
 () =>
 setCount(
  c => c + 1
 );

 return {

  count,

  increment

 };

}
```

---

# Hook Behavior

Expected:

```text
Initial Value = 0
```

---

After:

```text
increment()
```

---

Expected:

```text
count = 1
```

---

# Hook Testing Flow

```text
Execute Hook
       ↓

Call Hook Function
       ↓

Verify State
```

---

# Hook Testing Example

```javascript
const {
 result
}
=
renderHook(
 () => useCounter()
);
```

---

Check value:

```javascript
expect(
 result.current.count
)
.toBe(0);
```

---

Update:

```javascript
act(() => {

 result.current.increment();

});
```

---

Verify:

```javascript
expect(
 result.current.count
)
.toBe(1);
```

---

Pass.

---

# Why Hook Testing?

Many applications have:

```text
Authentication Hooks

API Hooks

Theme Hooks

Cart Hooks

Pagination Hooks
```

---

These contain critical logic.

---

Testing ensures:

```text
Reusable Logic Works
```

---

# Snapshot Testing

---

# 4. What is Snapshot Testing?

Definition:

> Snapshot Testing compares current UI output with a previously saved UI snapshot.

---

Think of it as:

```text
Taking Screenshot Of Component Structure
```

---

Not actual image.

---

React DOM structure.

---

# Example Component

```jsx
function Button() {

 return (

  <button>

   Save

  </button>

 );

}
```

---

Snapshot created:

```text
<button>
 Save
</button>
```

---

Stored automatically.

---

# Test

```javascript
import renderer
from
"react-test-renderer";
```

---

```javascript
test(

 "matches snapshot",

 () => {

  const tree =

   renderer

   .create(
    <Button />
   )

   .toJSON();

  expect(tree)
  .toMatchSnapshot();

 }

);
```

---

First Run

Creates snapshot file.

---

Example:

```text
Button.test.snap
```

---

# Second Run

Compares:

```text
Current Output
```

with

```text
Saved Snapshot
```

---

If changed:

```text
Test Fails
```

---

# Snapshot Example

Old Snapshot:

```html
<button>
 Save
</button>
```

---

Current:

```html
<button>
 Submit
</button>
```

---

Result:

```text
Snapshot Failed
```

---

UI changed.

---

# Snapshot Flow

```text
Render Component
       ↓

Generate Snapshot
       ↓

Store Snapshot
       ↓

Future Test Run
       ↓

Compare Snapshot
```

---

# Advantages

✔ Detects UI Changes

✔ Fast

✔ Easy

---

# Disadvantages

❌ Large Snapshots Hard To Maintain

❌ Can Produce False Positives

❌ Doesn't Test User Behavior

---

# Best Practice

Use snapshot testing:

```text
Small Stable Components
```

---

Not huge pages.

---

# Mock Functions

---

# 5. What Are Mock Functions?

Definition:

> Mock Functions replace real functions during testing.

---

Purpose:

```text
Avoid Real Dependencies
```

---

Example:

```javascript
sendEmail()
```

---

Real email should not be sent during testing.

---

Use mock.

---

# Jest Mock Function

```javascript
const mockFn =
jest.fn();
```

---

Call:

```javascript
mockFn();
```

---

Verify:

```javascript
expect(
 mockFn
)
.toHaveBeenCalled();
```

---

Pass.

---

# Example

Component:

```jsx
<button
 onClick={handleClick}
>
 Save
</button>
```

---

Test:

```javascript
const handleClick =
jest.fn();
```

---

Render:

```jsx
<Button

 onClick={
  handleClick
 }

/>
```

---

Click:

```javascript
userEvent.click(
 button
);
```

---

Verify:

```javascript
expect(
 handleClick
)
.toHaveBeenCalled();
```

---

Pass.

---

# Checking Call Count

```javascript
expect(
 mockFn
)
.toHaveBeenCalledTimes(
 1
);
```

---

# Mock Return Values

```javascript
const mockFn =
jest.fn();
```

---

```javascript
mockFn.mockReturnValue(
 "Hello"
);
```

---

Output:

```text
Hello
```

---

# Mock Implementation

```javascript
mockFn.mockImplementation(

 (a,b) => a+b

);
```

---

Now behaves like:

```javascript
(a,b)=>a+b
```

---

# Why Mock Functions?

---

Avoid:

```text
Database

Emails

Payments

External APIs
```

---

during testing.

---

# API Mocking

---

# 6. What is API Mocking?

Definition:

> API Mocking replaces real backend APIs with fake responses during testing.

---

Important concept.

---

Never call:

```text
Production APIs
```

during unit tests.

---

Problems:

```text
Slow

Unstable

Network Dependency

Rate Limits
```

---

Need:

```text
Mock APIs
```

---

# Example

Real API:

```http
GET /users
```

---

Returns:

```json
[
 {
  "id":1,
  "name":"John"
 }
]
```

---

During test:

```text
Fake Response
```

---

# Mocking Fetch

Component:

```jsx
useEffect(() => {

 fetchUsers();

}, []);
```

---

Mock:

```javascript
global.fetch =
jest.fn();
```

---

Return:

```javascript
fetch.mockResolvedValue({

 json: () =>
 Promise.resolve([

  {
   id:1,
   name:"John"
  }

 ])

});
```

---

Component believes:

```text
API Returned Data
```

---

No actual request made.

---

# API Mocking Flow

```text
Component
      ↓

Calls API
      ↓

Mock Intercepts
      ↓

Fake Response
      ↓

Component Updates
```

---

# Axios Mocking

Example:

```javascript
jest.mock(
 "axios"
);
```

---

Mock response:

```javascript
axios.get.mockResolvedValue({

 data: [

  {
   id:1
  }

 ]

});
```

---

Now:

```text
No Real Network Call
```

---

# Testing API Loading State

Component:

```jsx
Loading...
```

shown before data arrives.

---

Test:

```javascript
expect(

 screen.getByText(
  "Loading..."
 )

)
.toBeInTheDocument();
```

---

Then:

```javascript
await screen.findByText(
 "John"
);
```

---

Verify loaded state.

---

# Testing API Errors

Mock failure:

```javascript
fetch.mockRejectedValue(

 new Error(
  "API Failed"
 )

);
```

---

Verify:

```text
Error Message Shown
```

---

# MSW (Mock Service Worker)

Advanced interview topic.

---

Popular API mocking library:

```text
MSW
```

---

Intercepts requests:

```text
Browser Level
```

---

Very realistic.

---

Example:

```javascript
rest.get(

 "/users",

 (req,res,ctx) => {

  return res(

   ctx.json([
    {id:1}
   ])

  );

 }

);
```

---

Common in enterprise projects.

---

# Component Testing vs Hook Testing

---

# 7. Interview Question

## Component Testing

Tests:

```text
UI Components
```

---

Example:

```text
Buttons

Forms

Cards
```

---

## Hook Testing

Tests:

```text
Hook Logic
```

---

Example:

```text
useCounter()

useAuth()

useTheme()
```

---

# Comparison

| Feature | Component Testing | Hook Testing |
|----------|----------|----------|
| Tests UI | Yes | No |
| Tests Logic | Partial | Yes |
| Uses render() | Yes | No |
| Uses renderHook() | No | Yes |
| User Interaction | Yes | No |

---

# Snapshot Testing vs Component Testing

---

# Another Interview Question

Snapshot Testing:

```text
Compare UI Structure
```

---

Component Testing:

```text
Test User Behavior
```

---

Example:

Snapshot:

```text
Did UI Change?
```

---

Component Testing:

```text
Does Button Work?
```

---

# Common Mistakes

---

# Testing Internal State

Wrong:

```javascript
expect(
 state.count
)
.toBe(1);
```

---

Test UI output.

---

# Calling Real APIs

Wrong.

---

Always mock APIs.

---

# Overusing Snapshots

Wrong:

```text
Huge Page Snapshot
```

---

Creates maintenance issues.

---

Use snapshots carefully.

---

# Mocking Everything

Wrong.

---

Mock only:

```text
External Dependencies
```

---

# Best Practices

---

# 8. Production Recommendations

✔ Test user behavior

✔ Mock APIs

✔ Test custom hooks

✔ Use snapshots for stable UI

✔ Use mock functions for callbacks

✔ Test loading states

✔ Test error states

✔ Keep tests independent

✔ Prefer RTL over implementation testing

---

# Complete Testing Architecture

```text
React Application
       ↓

Components
       ↓
Component Testing

Custom Hooks
       ↓
Hook Testing

UI Structure
       ↓
Snapshot Testing

External Functions
       ↓
Mock Functions

Backend APIs
       ↓
API Mocking
```

---

# Interview Questions

---

### Q1. What is Component Testing?

```text
Testing React Components
```

---

### Q2. What is Hook Testing?

```text
Testing Custom Hooks
```

---

### Q3. What is Snapshot Testing?

```text
Comparing UI Against Saved Snapshot
```

---

### Q4. What is jest.fn()?

```text
Creates Mock Function
```

---

### Q5. Why use Mock Functions?

```text
Replace Real Dependencies
```

---

### Q6. Why Mock APIs?

```text
Fast

Reliable

Independent Tests
```

---

### Q7. What is renderHook()?

```text
Hook Testing Utility
```

---

### Q8. What is toMatchSnapshot()?

```text
Snapshot Assertion
```

---

### Q9. Snapshot Testing vs Component Testing?

Snapshot:

```text
UI Structure
```

---

Component:

```text
Behavior
```

---

### Q10. What is MSW?

```text
Mock Service Worker
```

---

Advanced API Mocking Tool.

---

# Memory Tricks

### Component Testing

```text
Test UI
```

---

### Hook Testing

```text
Test Logic
```

---

### Snapshot Testing

```text
Compare UI
```

---

### Mock Functions

```text
Fake Functions
```

---

### API Mocking

```text
Fake Backend
```

---

### jest.fn()

```text
Create Mock
```

---

### renderHook()

```text
Test Hook
```

---

### toMatchSnapshot()

```text
Compare Snapshot
```

---

# Quick Revision

```text
Testing Concepts
----------------

Component Testing
 ↓
 Test UI Components

Hook Testing
 ↓
 Test Custom Hooks

Snapshot Testing
 ↓
 Compare UI Structure

Mock Functions
 ↓
 Fake Functions

API Mocking
 ↓
 Fake API Responses

Tools:

Jest
 ↓
 Testing Framework

RTL
 ↓
 Component Testing

MSW
 ↓
 API Mocking

Best Practices:

✔ Test Behavior

✔ Mock APIs

✔ Test Hooks

✔ Test Loading States

✔ Test Error States

Remember:

Component Testing
 ↓
 UI

Hook Testing
 ↓
 Logic

Snapshot Testing
 ↓
 Structure

Mock Functions
 ↓
 Fake Functions

API Mocking
 ↓
 Fake Backend
```

---
