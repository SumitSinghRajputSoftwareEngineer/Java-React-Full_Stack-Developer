# useId Hook

> **Goal:** Understand useId from scratch, why React created it, Unique ID Generation, Accessibility (a11y), Form Label Linking, Server-Side Rendering (SSR), Hydration, Internal Working, Real-World Examples, Best Practices, Common Mistakes, Interview Questions, and React Internals.

---

# 1. What Problem Does useId Solve?

In HTML, many elements require:

```html
id
```

attribute.

---

Example:

```html
<input id="username" />
```

---

To connect a label:

```html
<label for="username">
  Username
</label>
```

---

Relationship:

```text
Label
  ↓
Input
```

---

Without matching IDs:

```text
Accessibility Breaks
```

---

Screen readers may not work properly.

---

Need:

```text
Unique IDs
```

---

React provides:

```jsx
useId()
```

---

# 2. Why Can't We Hardcode IDs?

Example:

```jsx
<input id="username" />
```

---

Works fine.

---

Until:

```jsx
<UserForm />
<UserForm />
<UserForm />
```

---

Rendered multiple times.

---

Generated HTML:

```html
<input id="username" />
<input id="username" />
<input id="username" />
```

---

Problem:

```text
Duplicate IDs
```

---

Invalid HTML.

---

Unexpected behavior.

---

Need:

```text
Unique ID Per Component
```

---

# 3. Traditional Solutions Before useId

Developers used:

```jsx
Math.random()
```

---

Example:

```jsx
const id =
Math.random();
```

---

Or:

```jsx
Date.now()
```

---

Or external libraries:

```text
uuid
nanoid
```

---

Worked.

---

But React had a bigger problem.

---

# 4. SSR Hydration Problem

Very important interview topic.

---

Suppose React server renders:

```html
<input id="123" />
```

---

Browser receives:

```html
<input id="123" />
```

---

Client re-renders:

```html
<input id="987" />
```

---

Mismatch.

---

React reports:

```text
Hydration Error
```

---

Need:

```text
Same ID
On Server And Client
```

---

React created:

```jsx
useId()
```

---

# 5. What is useId?

Definition:

> useId is a React Hook that generates unique, stable IDs that work correctly with both client-side rendering and server-side rendering.

---

Simple Meaning:

```text
Generate Unique IDs
Safely
```

---

# 6. Real-Life Example

Imagine:

```text
Employee IDs
```

---

Instead of:

```text
Employee
Employee
Employee
```

---

Company assigns:

```text
EMP-101

EMP-102

EMP-103
```

---

Every employee gets:

```text
Unique Identity
```

---

Exactly:

```jsx
useId()
```

---

# 7. Syntax

```jsx
const id = useId();
```

---

Returns:

```text
Unique String
```

---

Example:

```jsx
const id =
useId();
```

---

May produce:

```text
:r0:
```

---

Or:

```text
:r1:
```

---

React generates it automatically.

---

# First Example

---

# 8. Simple Example

```jsx
import {
 useId
}
from "react";

function App() {

 const id =
 useId();

 return (

  <input
   id={id}
  />

 );

}
```

---

Generated HTML:

```html
<input id=":r0:" />
```

---

React manages uniqueness.

---

# Label + Input Example

---

# 9. Most Common Use Case

Accessibility.

---

Example:

```jsx
function LoginForm() {

 const id =
 useId();

 return (

  <>
   <label
    htmlFor={id}
   >
    Username
   </label>

   <input
    id={id}
   />
  </>

 );

}
```

---

Relationship:

```text
Label
 ↓

Input
```

---

Accessibility works correctly.

---

# Why Accessibility Matters?

---

# 10. Screen Reader Support

Users may use:

```text
NVDA

JAWS

VoiceOver
```

---

Screen readers identify:

```text
Label ↔ Input
```

---

Using:

```text
htmlFor + id
```

---

Without it:

```text
Poor Accessibility
```

---

# Multiple Inputs Example

---

# 11. Problem

Need:

```text
Username

Password

Email
```

---

Each needs unique ID.

---

# Example

```jsx
function Form() {

 const usernameId =
 useId();

 const passwordId =
 useId();

 const emailId =
 useId();

 return (

  <>
   <label
    htmlFor={
     usernameId
    }
   >
    Username
   </label>

   <input
    id={usernameId}
   />

   <label
    htmlFor={
     passwordId
    }
   >
    Password
   </label>

   <input
    id={passwordId}
   />

   <label
    htmlFor={
     emailId
    }
   >
    Email
   </label>

   <input
    id={emailId}
   />
  </>

 );

}
```

---

All IDs:

```text
Unique
```

---

# Generating Related IDs

---

# 12. Common Pattern

Suppose:

```text
Input

Error Message

Help Text
```

---

Need related IDs.

---

Example:

```jsx
const id =
useId();
```

---

Create:

```jsx
const inputId =
`${id}-input`;

const errorId =
`${id}-error`;

const helpId =
`${id}-help`;
```

---

Generated:

```text
:r0:-input

:r0:-error

:r0:-help
```

---

Same base ID.

---

# Real World Example

---

# 13. Accessible Form Field

```jsx
function InputField() {

 const id =
 useId();

 return (

  <>
   <label
    htmlFor={id}
   >
    Name
   </label>

   <input
    id={id}
   />

   <span
    id={`${id}-help`}
   >
    Enter full name
   </span>
  </>

 );

}
```

---

Fully accessible.

---

# Multiple Component Instances

---

# 14. Example

```jsx
<UserForm />

<UserForm />

<UserForm />
```

---

Each receives:

```text
:r0:

:r1:

:r2:
```

---

No conflicts.

---

# Internal Working

---

# 15. How Does React Generate IDs?

React maintains:

```text
Internal ID Counter
```

---

For each component tree.

---

Generates:

```text
Stable IDs
```

---

During rendering.

---

# 16. Why Stable?

Because React needs:

```text
Same ID

On Server

And Client
```

---

To avoid hydration errors.

---

# SSR Example

---

# 17. Server Rendering

Server produces:

```html
<input id=":r0:" />
```

---

Browser receives:

```html
<input id=":r0:" />
```

---

Client React renders:

```html
<input id=":r0:" />
```

---

Perfect match.

---

Hydration succeeds.

---

# Hydration Explained

---

# 18. What is Hydration?

Server sends:

```html
Ready HTML
```

---

Browser displays:

```text
Page Visible
```

immediately.

---

Then React attaches:

```text
Events

State

Logic
```

---

This process is:

```text
Hydration
```

---

# 19. Hydration Mismatch

If IDs differ:

```text
Server: :r0:

Client: :r9:
```

---

React warns:

```text
Hydration Failed
```

---

useId prevents this.

---

# useId vs Random IDs

---

# 20. Math.random()

```jsx
const id =
Math.random();
```

---

Problem:

```text
Different Every Render
```

---

SSR unsafe.

---

# 21. useId()

```jsx
const id =
useId();
```

---

Benefits:

```text
Stable

Predictable

SSR Safe
```

---

# useId vs UUID

---

# 22. UUID Example

```jsx
import { v4 }
from "uuid";
```

---

Generate:

```text
2f34d7ab...
```

---

Useful.

---

But:

```text
Not SSR-Aware
```

---

# 23. useId

Specifically designed for:

```text
React Components
```

---

And:

```text
SSR Hydration
```

---

# Important Limitation

---

# 24. Not For List Keys

Very important interview question.

---

Wrong:

```jsx
{
 users.map(user => (

  <li
   key={useId()}
  >
   {user.name}
  </li>

 ))
}
```

---

Violates:

```text
Rules Of Hooks
```

---

And unstable.

---

# 25. Correct Key

```jsx
<li key={user.id}>
```

---

Always use:

```text
Actual Data IDs
```

---

Not:

```jsx
useId()
```

---

# Another Limitation

---

# 26. Not For Database IDs

Wrong:

```jsx
const userId =
useId();
```

---

Save into database.

---

Bad idea.

---

Why?

```text
React IDs Are UI IDs
```

---

Not business identifiers.

---

Use:

```text
UUID

Database IDs
```

---

Instead.

---

# Common Use Cases

---

# 27. Form Fields

Examples:

```text
Input

Label

Checkbox

Radio Button
```

---

Most common.

---

# 28. Accessibility

Examples:

```text
aria-labelledby

aria-describedby
```

---

Need IDs.

---

# 29. Component Libraries

Examples:

```text
Material UI

Ant Design

Chakra UI
```

---

Frequently use:

```jsx
useId()
```

internally.

---

# Accessibility Example

---

# 30. aria-describedby

```jsx
const id =
useId();
```

---

```jsx
<input
 aria-describedby={
 `${id}-help`
 }
/>
```

---

```jsx
<p
 id={`${id}-help`}
>
 Enter email
</p>
```

---

Screen readers understand relationship.

---

# Common Mistakes

---

# 31. Using For Keys

Wrong:

```jsx
key={useId()}
```

---

Never do this.

---

# 32. Calling Inside Loop

Wrong:

```jsx
users.map(() => {

 useId();

});
```

---

Violates:

```text
Rules Of Hooks
```

---

# 33. Using As Database ID

Wrong.

---

useId is:

```text
UI Identifier
```

---

Not:

```text
Business Identifier
```

---

# Best Practices

---

# 34. Use For Accessibility

Best use case:

```text
Label ↔ Input
```

---

# 35. Use For SSR Applications

Examples:

```text
Next.js

Remix

Gatsby
```

---

Prevents hydration issues.

---

# 36. Use For Related IDs

Example:

```jsx
inputId

errorId

helpId
```

---

Derived from same base.

---

# React Fiber Internals

---

# 37. Hook Storage

React Fiber stores:

```text
ID Metadata
```

---

For each:

```jsx
useId()
```

---

Ensures:

```text
Consistent Generation
```

---

Across renders.

---

# 38. Why React Needed This Hook?

Before React 18:

```text
SSR ID Problems
```

were common.

---

React 18 introduced:

```jsx
useId()
```

---

Specifically to solve:

```text
Hydration Mismatch
```

---

# Comparison

---

# 39. useId vs useRef

### useRef

```text
Store Mutable Reference
```

---

### useId

```text
Generate Unique ID
```

---

Different purposes.

---

# 40. useId vs UUID

| Feature | useId | UUID |
|----------|---------|---------|
| React Aware | Yes | No |
| SSR Safe | Yes | No |
| Accessibility | Yes | Possible |
| Database IDs | No | Yes |
| UI IDs | Yes | Yes |

---

# Real World Production Example

---

# 41. Custom Input Component

```jsx
function InputField({

 label

}) {

 const id =
 useId();

 return (

  <>
   <label
    htmlFor={id}
   >
    {label}
   </label>

   <input
    id={id}
   />
  </>

 );

}
```

---

Usage:

```jsx
<InputField
 label="Username"
/>

<InputField
 label="Password"
/>
```

---

Generated IDs:

```text
:r0:

:r1:
```

---

No collisions.

---

# Interview Questions

---

### Q1. What is useId?

```text
Hook That Generates
Unique Stable IDs
```

---

### Q2. Why Was useId Introduced?

```text
Accessibility

SSR Hydration Safety
```

---

### Q3. Can useId Be Used For Keys?

```text
No
```

---

Use actual data IDs.

---

### Q4. Is useId SSR Safe?

```text
Yes
```

---

Main reason it exists.

---

### Q5. Most Common Use Case?

```text
Label + Input Linking
```

---

### Q6. Can useId Generate Database IDs?

```text
No
```

---

Only UI IDs.

---

### Q7. Difference Between useId and UUID?

```text
useId
 ↓
React UI IDs

UUID
 ↓
General Unique IDs
```

---

### Q8. Can useId Prevent Hydration Errors?

```text
Yes
```

---

One of its major benefits.

---

### Q9. What Accessibility Attribute Commonly Uses useId?

```text
htmlFor

aria-describedby

aria-labelledby
```

---

### Q10. Which React Version Introduced useId?

```text
React 18
```

---

# Memory Tricks

### useRef

```text
Store Reference
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

### useDebugValue

```text
Debug Custom Hooks
```

---

### useId

```text
Generate Unique UI IDs
```

---

### Main Purpose

```text
Accessibility
+
SSR Safety
```

---

### Rule

```text
Need Label ↔ Input Link?
 ↓

useId()
```

---

# Quick Revision

```text
useId
-----

Purpose:

Generate Unique Stable IDs

Syntax:

const id =
useId();

Most Common Uses:

✔ Label + Input

✔ Accessibility

✔ aria-describedby

✔ aria-labelledby

✔ SSR Applications

Advantages:

✔ Unique IDs

✔ SSR Safe

✔ Prevents Hydration Errors

✔ Accessibility Friendly

Disadvantages:

✘ Not For Keys

✘ Not For Database IDs

✘ UI Only

Remember:

useId
 ↓

Accessibility

Unique IDs

SSR Safe

Hydration Friendly
```

---

# Final Advanced Hooks Map

```text
useRef
 ↓
Mutable References

useLayoutEffect
 ↓
DOM Work Before Paint

useImperativeHandle
 ↓
Customize Ref API

useDebugValue
 ↓
Debug Custom Hooks

useId
 ↓
Unique Stable IDs
For Accessibility
And SSR
```

---

# Complete Hooks Journey So Far

```text
Basic Hooks
-----------

useState
 ↓
State Management

useEffect
 ↓
Side Effects

useContext
 ↓
Share Data


Additional Hooks
----------------

useReducer
 ↓
Complex State

useRef
 ↓
Mutable References

useMemo
 ↓
Memoize Values

useCallback
 ↓
Memoize Functions

useLayoutEffect
 ↓
DOM Work Before Paint

useImperativeHandle
 ↓
Customize Ref

useDebugValue
 ↓
Debug Hooks

useId
 ↓
Unique Stable IDs


Next Topic
----------

Custom Hooks
```

---