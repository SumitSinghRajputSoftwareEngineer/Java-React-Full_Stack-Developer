# Context API

> **Goal:** Master React Context API completely — understand why Context exists, what problems it solves, `createContext()`, Provider, Consumer, `useContext()`, Context lifecycle, Context performance optimization, Multiple Contexts, internal working, real-world examples, common mistakes, best practices, and interview questions.

---

# 1. What is Context API?

Context API is React's built-in mechanism for:

```text
Sharing Data
Across Components
Without Prop Drilling
```

---

Imagine:

```text
User

Theme

Language

Authentication

Cart Count
```

---

Needed in many components.

---

Instead of:

```text
Passing Props
Through Every Component
```

---

Use:

```text
Context API
```

---

# Definition

> Context API allows data to be shared globally across a component tree without manually passing props through every level.

---

# 2. Why Context API Exists?

Consider:

```text
App
 ↓

Navbar
 ↓

Menu
 ↓

Profile
```

---

Need:

```text
Current User
```

---

Without Context:

```jsx
<App user={user}/>
```

---

```jsx
<Navbar user={user}/>
```

---

```jsx
<Menu user={user}/>
```

---

```jsx
<Profile user={user}/>
```

---

Even though:

```text
Navbar

Menu
```

Don't need user.

---

Only:

```text
Profile
```

Needs it.

---

Problem:

```text
Prop Drilling
```

---

# 3. What is Prop Drilling?

Passing props through intermediate components.

---

Visualization:

```text
App
 ↓ user

Navbar
 ↓ user

Menu
 ↓ user

Profile
```

---

Problems:

```text
Extra Code

Hard Maintenance

Poor Scalability

Unnecessary Props
```

---

Need better solution.

---

# Context Solution

```text
Provider

   ↓

Any Component
```

---

No intermediate props.

---

Visualization:

```text
App
 ↓

Context

 ↓

Profile
```

---

Direct access.

---

# Internal Architecture

---

# 4. Context Components

Context API consists of:

```text
1. createContext()

2. Provider

3. Consumer

4. useContext()
```

---

Visualization:

```text
createContext()
      ↓

Provider
      ↓

Consumer
or
useContext()
```

---

# createContext()

---

# 5. What is createContext()?

Creates a new Context object.

---

Syntax:

```jsx
const UserContext =
createContext();
```

---

Import:

```jsx
import {

 createContext

}
from "react";
```

---

# Example

```jsx
const ThemeContext =

createContext();
```

---

React creates:

```text
Theme Context Object
```

---

Containing:

```text
Provider

Consumer
```

---

Internally:

```jsx
{
 Provider,
 Consumer
}
```

---

# 6. Default Value

Example:

```jsx
const UserContext =

createContext(
 "Guest"
);
```

---

Default:

```text
Guest
```

---

Used when:

```text
No Provider Exists
```

---

Example:

```jsx
const user =

useContext(
 UserContext
);
```

---

Output:

```text
Guest
```

---

# Provider

---

# 7. What is Provider?

Provider supplies data.

---

Syntax:

```jsx
<UserContext.Provider

 value={data}

>

  Components

</UserContext.Provider>
```

---

Visualization:

```text
Provider

  ↓

All Child Components
```

---

# Example

```jsx
<UserContext.Provider

 value="Sumit"

>

 <App/>

</UserContext.Provider>
```

---

Now all descendants can access:

```text
Sumit
```

---

# 8. Provider Value

Provider can pass:

```text
String

Number

Object

Array

Function

State
```

---

Example

```jsx
<UserContext.Provider

 value={{

   name:"Sumit",

   age:25

 }}

>

</UserContext.Provider>
```

---

Consumers receive:

```jsx
{
 name:"Sumit",
 age:25
}
```

---

# Real World Example

---

# 9. Theme Provider

```jsx
<ThemeContext.Provider

 value="dark"

>

 <App/>

</ThemeContext.Provider>
```

---

Every component can know:

```text
Dark Theme
```

---

# 10. Auth Provider

```jsx
<AuthContext.Provider

 value={user}

>

 <App/>

</AuthContext.Provider>
```

---

Every component can access:

```text
Logged User
```

---

# Consumer

---

# 11. What is Consumer?

Old way to consume Context.

---

Before Hooks existed.

---

Syntax:

```jsx
<UserContext.Consumer>

 {(value) => (

   <h1>{value}</h1>

 )}

</UserContext.Consumer>
```

---

Output:

```text
Sumit
```

---

Works.

---

But verbose.

---

# Visualization

```text
Provider
   ↓

Consumer
```

---

Receives value.

---

# 12. Example

Provider:

```jsx
<UserContext.Provider

 value="React"

>

 <App/>

</UserContext.Provider>
```

---

Consumer:

```jsx
<UserContext.Consumer>

 {(data)=>

  <h1>{data}</h1>

 }

</UserContext.Consumer>
```

---

Output:

```text
React
```

---

# Problem With Consumer

Too much nesting.

---

Example:

```jsx
<User.Consumer>

 {(user)=>(

  <Theme.Consumer>

   {(theme)=>(

    ...

   )}

  </Theme.Consumer>

 )}

</User.Consumer>
```

---

Messy.

---

Hooks solved this.

---

# useContext()

---

# 13. What is useContext()?

Modern way to consume Context.

---

Syntax:

```jsx
const value =

useContext(
 ContextName
);
```

---

Import:

```jsx
import {

 useContext

}
from "react";
```

---

# Example

```jsx
const UserContext =

createContext();
```

---

Provider:

```jsx
<UserContext.Provider

 value="Sumit"

>

 <Profile/>

</UserContext.Provider>
```

---

Consumer:

```jsx
function Profile() {

 const user =

 useContext(
  UserContext
 );

 return (

  <h1>
   {user}
  </h1>

 );

}
```

---

Output:

```text
Sumit
```

---

Much cleaner.

---

# useContext Internal Working

---

# 14. What Happens Internally?

React stores:

```text
Current Context Value
```

---

Inside:

```text
Provider
```

---

When component calls:

```jsx
useContext(
 UserContext
)
```

---

React:

```text
Looks Up
Nearest Provider
```

---

Returns value.

---

Visualization:

```text
Provider

 value="Dark"

      ↓

Component

 useContext()
      ↓

 Returns "Dark"
```

---

# Context Update Flow

---

# 15. Example

```jsx
const [theme,
setTheme]
=
useState("light");
```

---

Provider:

```jsx
<ThemeContext.Provider

 value={theme}

>

</ThemeContext.Provider>
```

---

Theme changes:

```jsx
setTheme("dark");
```

---

React:

```text
Provider Updates
        ↓

Context Updates
        ↓

Consumers Re-render
```

---

Important.

---

# Passing State Through Context

---

# 16. Real Example

```jsx
const [user,
setUser]
=
useState(null);
```

---

Provider:

```jsx
<UserContext.Provider

 value={{

  user,

  setUser

 }}

>

</UserContext.Provider>
```

---

Consumer:

```jsx
const {

 user,

 setUser

}
=
useContext(
 UserContext
);
```

---

Can:

```text
Read State

Update State
```

---

Globally.

---

# Context Performance Problem

---

# 17. Biggest Context Drawback

Suppose:

```jsx
value={{
 theme,
 setTheme
}}
```

---

Theme changes.

---

React re-renders:

```text
ALL Consumers
```

---

Even if:

```text
Consumer Doesn't Need Theme
```

---

Potential performance issue.

---

# Visualization

```text
Provider Updated

     ↓

Consumer A

Consumer B

Consumer C

Consumer D
```

---

All checked.

---

# Example Problem

---

# 18. Large Application

```text
Auth Context
```

Contains:

```text
User

Role

Permissions

Token
```

---

Token updates.

---

Entire context consumers:

```text
Re-render
```

---

Expensive.

---

# Context Performance Optimization

---

# 19. Split Contexts

Bad:

```jsx
AppContext
```

Contains:

```text
User

Theme

Language

Cart
```

---

Huge context.

---

Better:

```text
UserContext

ThemeContext

LanguageContext

CartContext
```

---

Smaller updates.

---

# 20. Memoize Context Value

Wrong:

```jsx
<UserContext.Provider

 value={{
  user
 }}

>
```

---

New object created every render.

---

Causes:

```text
Extra Re-renders
```

---

Better:

```jsx
const value =
useMemo(() => {

 return {

  user

 };

}, [user]);
```

---

Provider:

```jsx
<UserContext.Provider

 value={value}

>
```

---

Much better.

---

# 21. useCallback With Context

Suppose:

```jsx
const login = () => {};
```

---

New function every render.

---

Use:

```jsx
const login =
useCallback(() => {

}, []);
```

---

Stable reference.

---

Less re-rendering.

---

# 22. Memoized Consumers

Use:

```jsx
React.memo()
```

---

To reduce unnecessary renders.

---

Example:

```jsx
export default
React.memo(
 Profile
);
```

---

Useful in large apps.

---

# Multiple Contexts

---

# 23. Why Multiple Contexts?

Applications usually have:

```text
User

Theme

Language

Cart
```

---

One context is not enough.

---

Need:

```text
Multiple Contexts
```

---

# Example

Create:

```jsx
UserContext
```

---

Create:

```jsx
ThemeContext
```

---

Create:

```jsx
LanguageContext
```

---

Independent.

---

# 24. Multiple Providers

```jsx
<UserContext.Provider

 value={user}

>

 <ThemeContext.Provider

  value={theme}

 >

  <App/>

 </ThemeContext.Provider>

</UserContext.Provider>
```

---

Perfectly valid.

---

# Visualization

```text
User Provider
      ↓

Theme Provider
      ↓

Language Provider
      ↓

App
```

---

# Consuming Multiple Contexts

---

# 25. Example

```jsx
const user =
useContext(
 UserContext
);
```

---

```jsx
const theme =
useContext(
 ThemeContext
);
```

---

Now component receives:

```text
User

Theme
```

---

# Real World Context Structure

---

# 26. Example

```text
AuthContext

ThemeContext

LanguageContext

NotificationContext
```

---

Very common.

---

# Context vs Props

---

# 27. Props

Best for:

```text
Parent → Child
```

---

Simple communication.

---

# Example

```jsx
<Product

 name="Laptop"

/>
```

---

Easy.

---

# 28. Context

Best for:

```text
Shared Global Data
```

---

Example:

```text
Logged User

Theme

Language
```

---

Across many components.

---

# Context vs Redux

---

# 29. Context API

Provides:

```text
Data Sharing
```

---

Built into React.

---

Small-to-medium apps.

---

# 30. Redux

Provides:

```text
Centralized State Management
```

---

Features:

```text
Reducers

Middleware

DevTools

Predictable Updates
```

---

Better for:

```text
Large Applications
```

---

# Context vs Redux Table

| Feature | Context API | Redux |
|----------|----------|----------|
| Built Into React | Yes | No |
| Easy Setup | Yes | Medium |
| Global Data Sharing | Yes | Yes |
| Performance | Medium | High |
| Large Apps | Medium | Excellent |
| DevTools | Limited | Excellent |

---

# Common Mistakes

---

# 31. Using Context For Everything

Wrong.

---

Context should not store:

```text
Every State
```

---

Can cause:

```text
Performance Problems
```

---

# 32. Huge Context Object

Wrong:

```jsx
{
 user,
 cart,
 orders,
 theme,
 language
}
```

---

Massive re-renders.

---

Split contexts.

---

# 33. Forgetting useMemo

Wrong:

```jsx
value={{
 user
}}
```

---

Creates new object every render.

---

Use:

```jsx
useMemo()
```

---

# Best Practices

---

# 34. Use Context For

```text
Authentication

Theme

Language

User Preferences
```

---

Excellent use cases.

---

# 35. Split Contexts

Instead of:

```text
One Giant Context
```

---

Create:

```text
Many Small Contexts
```

---

# 36. Memoize Values

Always consider:

```jsx
useMemo()

useCallback()
```

---

For Provider values.

---

# 37. Keep Context Focused

Each context should have:

```text
Single Responsibility
```

---

# Interview Questions

---

### Q1. What problem does Context API solve?

```text
Prop Drilling
```

---

### Q2. What are the parts of Context API?

```text
createContext()

Provider

Consumer

useContext()
```

---

### Q3. What does Provider do?

```text
Supplies Data
```

---

### Q4. What does Consumer do?

```text
Reads Context
```

---

### Q5. What replaced Consumer?

```text
useContext()
```

---

### Q6. What causes Context re-renders?

```text
Provider Value Change
```

---

### Q7. How do you optimize Context?

```text
Split Contexts

useMemo

useCallback
```

---

### Q8. Can we have multiple contexts?

```text
Yes
```

---

### Q9. Context API vs Redux?

Context:

```text
Data Sharing
```

---

Redux:

```text
Full State Management
```

---

### Q10. What is Prop Drilling?

```text
Passing Props Through
Many Components
```

---

# Memory Tricks

### createContext()

```text
Create Context
```

---

### Provider

```text
Provide Data
```

---

### Consumer

```text
Consume Data
```

---

### useContext()

```text
Read Context
```

---

### Context Purpose

```text
Avoid Prop Drilling
```

---

### Optimization

```text
Split Contexts

useMemo

useCallback
```

---

### Multiple Contexts

```text
User

Theme

Language

Cart
```

---

# Complete Flow

```text
createContext()
        ↓

Provider
        ↓

useContext()
        ↓

Consumer Receives Data
        ↓

Provider Updates
        ↓

Consumer Re-renders
```

---

# Quick Revision

```text
Context API
-----------

Purpose:
Avoid Prop Drilling

Components:

createContext()
      ↓

Provider
      ↓

Consumer
or
useContext()

Provider:
Supplies Data

useContext:
Reads Data

Optimization:

✔ Split Contexts

✔ useMemo

✔ useCallback

✔ React.memo

Use Cases:

✔ Theme

✔ Authentication

✔ Language

✔ User Preferences

Remember:

Props
 ↓
Simple Communication

Context
 ↓
Shared Data

Redux
 ↓
Large Scale State Management
```

---