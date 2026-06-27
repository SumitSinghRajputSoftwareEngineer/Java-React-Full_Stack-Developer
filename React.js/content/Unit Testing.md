# Unit Testing in React

> **Goal:** Master React Unit Testing completely — understand why testing is important, testing pyramid, Jest, React Testing Library (RTL), test structure, assertions, mocking, testing components, hooks, events, API calls, best practices, real-world examples, and interview questions.

---

# 1. What is Testing?

Testing verifies that our application behaves as expected.

---

Example:

User clicks:

```text
Login Button
```

---

Expected:

```text
Login API Called
```

---

Testing ensures:

```text
Application Works Correctly
```

---

before users use it.

---

# Why Testing is Important?

Without testing:

```text
Manual Testing
```

every time.

---

Problems:

```text
Time Consuming

Human Errors

Regression Bugs

Unstable Releases
```

---

Testing helps:

```text
Catch Bugs Early
```

---

# Example

Developer changes:

```jsx
Login Component
```

---

Accidentally breaks:

```jsx
Submit Button
```

---

Tests immediately fail.

---

Bug caught early.

---

# Benefits of Testing

✔ Improves Code Quality

✔ Prevents Regression Bugs

✔ Easier Refactoring

✔ Faster Releases

✔ More Confidence

✔ Better Maintainability

---

# Types of Testing

---

# 2. Testing Pyramid

Important interview topic.

---

```text
          E2E
           ▲
         Integration
           ▲
          Unit
```

---

# Unit Testing

Tests:

```text
Single Function

Single Component

Single Hook
```

---

Example:

```jsx
Button Component
```

---

# Integration Testing

Tests:

```text
Multiple Components Together
```

---

Example:

```text
Login Form + API
```

---

# End-to-End Testing

Tests:

```text
Entire Application
```

---

Example:

```text
Login
 ↓
Dashboard
 ↓
Logout
```

---

Tools:

```text
Cypress

Playwright
```

---

React mainly uses:

```text
Unit Testing
```

---

# What is Jest?

---

# 3. Introduction to Jest

Jest is:

```text
JavaScript Testing Framework
```

created by:

:contentReference[oaicite:0]{index=0}

---

Most popular React testing framework.

---

Used for:

```text
Running Tests

Assertions

Mocking

Coverage Reports
```

---

# Installation

```bash
npm install --save-dev jest
```

---

CRA projects already include Jest.

---

# Basic Test

File:

```jsx
sum.js
```

---

```javascript
function sum(a, b) {

 return a + b;

}

export default sum;
```

---

Test:

```javascript
import sum from "./sum";

test(

 "adds numbers",

 () => {

  expect(
   sum(2,3)
  ).toBe(5);

 }

);
```

---

Output:

```text
PASS
```

---

# Jest Test Structure

---

# 4. Anatomy of a Test

Example:

```javascript
test(

 "adds numbers",

 () => {

  expect(
   sum(2,3)
  ).toBe(5);

 }

);
```

---

# test()

Defines test case.

---

```javascript
test(
 "description",
 callback
);
```

---

Example:

```javascript
test(
 "button renders",
 () => {}
);
```

---

# expect()

Creates assertion.

---

Example:

```javascript
expect(value)
```

---

# Matcher

Checks result.

---

Example:

```javascript
toBe()

toEqual()

toContain()
```

---

# Visualization

```text
Test
 ↓

Action
 ↓

Assertion
 ↓

Pass/Fail
```

---

# Common Jest Matchers

---

# 5. Important Assertions

---

## toBe()

Primitive values.

---

Example:

```javascript
expect(5).toBe(5);
```

---

Pass.

---

# toEqual()

Objects and arrays.

---

Example:

```javascript
expect(

 {id:1}

).toEqual(

 {id:1}

);
```

---

# toContain()

Check element exists.

---

Example:

```javascript
expect(
 ["A","B"]
)
.toContain("A");
```

---

# toBeTruthy()

Truthy values.

---

Example:

```javascript
expect(true)
.toBeTruthy();
```

---

# toBeFalsy()

Falsy values.

---

Example:

```javascript
expect(false)
.toBeFalsy();
```

---

# describe()

Groups tests.

---

Example:

```javascript
describe(

 "Math Tests",

 () => {

  test(...);

  test(...);

 }

);
```

---

Output:

```text
Math Tests

 ✓ Test1
 ✓ Test2
```

---

# beforeEach()

Runs before every test.

---

Example:

```javascript
beforeEach(() => {

 console.log(
  "Before Test"
 );

});
```

---

Useful for setup.

---

# afterEach()

Runs after every test.

---

Example:

```javascript
afterEach(() => {

 cleanup();

});
```

---

# Mocking in Jest

---

# 6. What is Mocking?

Real APIs:

```text
Slow

Unstable

External Dependency
```

---

Testing should not call:

```text
Real Backend
```

---

Use:

```text
Mocks
```

---

# Example

Function:

```javascript
fetchUsers()
```

---

Instead of real API:

```javascript
jest.fn()
```

---

Mock returns:

```javascript
[
 {id:1}
]
```

---

Instantly.

---

# Mock Function

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

Check:

```javascript
expect(mockFn)
.toHaveBeenCalled();
```

---

Pass.

---

# Mock Return Value

```javascript
const mockFn =
jest.fn();

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

# React Testing Library (RTL)

---

# 7. What is React Testing Library?

Official React testing utility.

---

Created by:

:contentReference[oaicite:1]{index=1}

---

RTL focuses on:

```text
Testing User Behavior
```

---

Instead of:

```text
Testing Implementation Details
```

---

# Philosophy

Important interview topic.

---

Bad:

```text
Test Internal State
```

---

Good:

```text
Test What User Sees
```

---

RTL encourages:

```text
User-Centric Testing
```

---

# Installation

```bash
npm install

@testing-library/react

@testing-library/jest-dom
```

---

CRA already includes RTL.

---

# First React Component Test

---

# 8. Example Component

```jsx
function Welcome() {

 return (
  <h1>
   Hello React
  </h1>
 );

}
```

---

Test:

```jsx
import {

 render,
 screen

}
from
"@testing-library/react";

import Welcome
from "./Welcome";

test(

 "renders heading",

 () => {

  render(
   <Welcome />
  );

  expect(

   screen.getByText(
    "Hello React"
   )

  ).toBeInTheDocument();

 }

);
```

---

Pass.

---

# render()

Creates virtual DOM.

---

Example:

```javascript
render(
 <Component />
);
```

---

Component rendered.

---

# screen

Provides querying methods.

---

Example:

```javascript
screen.getByText()
```

---

Searches UI.

---

# Query Methods

---

# 9. getByText()

Find text.

---

Example:

```javascript
screen.getByText(
 "Login"
);
```

---

# getByRole()

Most recommended.

---

Example:

```javascript
screen.getByRole(
 "button"
);
```

---

# getByPlaceholderText()

Example:

```javascript
screen
.getByPlaceholderText(
 "Email"
);
```

---

# getByLabelText()

Example:

```javascript
screen.getByLabelText(
 "Password"
);
```

---

# Query Priority

Best practice:

```text
Role

Label

Placeholder

Text
```

---

Closest to user behavior.

---

# Testing Button Clicks

---

# 10. User Events

Component:

```jsx
function Counter() {

 const [count,
 setCount] =
 useState(0);

 return (

  <>

   <p>
    {count}
   </p>

   <button

    onClick={() =>
     setCount(
      count+1
     )
    }

   >

    Increment

   </button>

  </>

 );

}
```

---

Test:

```jsx
import userEvent
from
"@testing-library/user-event";
```

---

```jsx
test(

 "increments count",

 async () => {

  render(
   <Counter />
  );

  const button =

   screen.getByRole(
    "button"
   );

  await userEvent.click(
   button
  );

  expect(

   screen.getByText("1")

  ).toBeInTheDocument();

 }

);
```

---

Flow:

```text
Render
 ↓

Click
 ↓

State Update
 ↓

Verify UI
```

---

# Testing Input Fields

---

# 11. Form Testing

Component:

```jsx
<input
 placeholder="Name"
/>
```

---

Test:

```jsx
const input =

screen
.getByPlaceholderText(
 "Name"
);
```

---

Type:

```jsx
await userEvent.type(

 input,

 "John"

);
```

---

Verify:

```jsx
expect(
 input
)
.toHaveValue(
 "John"
);
```

---

Pass.

---

# Testing Props

---

# 12. Example

Component:

```jsx
function User({
 name
}) {

 return (
  <h1>{name}</h1>
 );

}
```

---

Test:

```jsx
render(

 <User
  name="John"
 />

);
```

---

Verify:

```jsx
expect(

 screen.getByText(
  "John"
 )

)
.toBeInTheDocument();
```

---

Pass.

---

# Testing API Calls

---

# 13. Mocking APIs

Component:

```jsx
useEffect(() => {

 fetchUsers();

}, []);
```

---

Never call real API.

---

Mock:

```javascript
global.fetch =
jest.fn();
```

---

Return:

```javascript
Promise.resolve({

 json: () =>
 Promise.resolve([
  {id:1}
 ])

});
```

---

Test predictable behavior.

---

# Async Testing

---

# 14. waitFor()

Used for:

```text
API Calls

Async Operations
```

---

Example:

```javascript
await waitFor(() => {

 expect(

  screen.getByText(
   "John"
  )

 ).toBeInTheDocument();

});
```

---

Waits until condition passes.

---

# findByText()

Async version.

---

Example:

```javascript
await screen.findByText(
 "John"
);
```

---

Automatically waits.

---

# Testing Hooks

---

# 15. Custom Hook Example

Hook:

```javascript
function useCounter() {

 const [count,
 setCount] =
 useState(0);

 return {

  count,

  increment:
   () =>
   setCount(c=>c+1)

 };

}
```

---

Can be tested.

---

Verify:

```text
Count Changes
```

---

after calling:

```text
increment()
```

---

# Code Coverage

---

# 16. Coverage Reports

Shows:

```text
Tested Code

Untested Code
```

---

Run:

```bash
npm test -- --coverage
```

---

Output:

```text
Statements

Branches

Functions

Lines
```

---

Coverage example:

```text
85%
```

---

Higher is better.

---

# Common Mistakes

---

# 17. Testing Implementation Details

Wrong:

```text
Testing Internal State
```

---

Correct:

```text
Test UI Behavior
```

---

# Using getByText Everywhere

Prefer:

```javascript
getByRole()
```

---

Closer to user behavior.

---

# Calling Real APIs

Wrong.

---

Always:

```text
Mock APIs
```

---

# Testing CSS Classes

Usually unnecessary.

---

Test:

```text
Behavior
```

---

Not implementation.

---

# Best Practices

---

# 18. Production Recommendations

✔ Test user behavior

✔ Use RTL queries

✔ Prefer getByRole()

✔ Mock API calls

✔ Write meaningful test names

✔ Keep tests independent

✔ Use coverage reports

✔ Test edge cases

✔ Avoid implementation testing

---

# Jest vs React Testing Library

---

# 19. Most Important Interview Question

## Jest

Provides:

```text
Test Runner

Assertions

Mocking

Coverage
```

---

## RTL

Provides:

```text
Component Rendering

User Interaction

DOM Queries
```

---

# Comparison

| Feature | Jest | RTL |
|----------|----------|----------|
| Test Runner | Yes | No |
| Assertions | Yes | No |
| Mocking | Yes | No |
| Render Components | No | Yes |
| Simulate User Actions | No | Yes |
| Query DOM | No | Yes |

---

Usually used together.

---

# Complete Testing Flow

```text
React Component
        ↓

Render Using RTL
        ↓

User Action
        ↓

Button Click
        ↓

State Change
        ↓

Assertion
        ↓

Pass / Fail
```

---

# Interview Questions

---

### Q1. What is Jest?

```text
JavaScript Testing Framework
```

---

### Q2. What is React Testing Library?

```text
Library For Testing React Components
```

---

### Q3. Difference between Jest and RTL?

Jest:

```text
Testing Framework
```

---

RTL:

```text
React Testing Utility
```

---

### Q4. What does render() do?

```text
Renders Component
Into Test DOM
```

---

### Q5. Most recommended RTL query?

```javascript
getByRole()
```

---

### Q6. Why mock APIs?

```text
Fast

Reliable

Independent
```

---

### Q7. What is userEvent?

```text
Simulates Real User Actions
```

---

### Q8. What is waitFor()?

```text
Waits For Async Updates
```

---

### Q9. What is code coverage?

```text
Percentage Of Code
Covered By Tests
```

---

### Q10. What should tests verify?

```text
User Behavior
```

---

Not implementation details.

---

# Memory Tricks

### Jest

```text
Run Tests
```

---

### RTL

```text
Test UI
```

---

### render()

```text
Render Component
```

---

### screen

```text
Find Elements
```

---

### userEvent

```text
Simulate User
```

---

### Mocking

```text
Fake Dependencies
```

---

### waitFor()

```text
Wait For Async
```

---

### Coverage

```text
Measure Testing
```

---

# Quick Revision

```text
Unit Testing
------------

Jest
 ↓
 Test Runner
 Assertions
 Mocking
 Coverage

RTL
 ↓
 Render Components
 Query DOM
 User Events

Common Functions:

test()

describe()

expect()

render()

screen.getByRole()

userEvent.click()

waitFor()

Best Practice:

Test User Behavior

NOT Internal State

Benefits:

✔ Bug Detection

✔ Safe Refactoring

✔ Better Quality

✔ Faster Development

Remember:

Jest
 ↓
 Testing Framework

RTL
 ↓
 React Component Testing
```

---
