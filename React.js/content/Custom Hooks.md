# Custom Hooks

> **Goal:** Understand Custom Hooks from scratch, why React created them, Code Reusability, Logic Sharing, Hook Composition, Internal Working, Real-World Examples, Best Practices, Common Mistakes, Advanced Patterns, Interview Questions, and React Internals.

---

# 1. What Problem Do Custom Hooks Solve?

Imagine you have two components.

---

Component 1:

```jsx
function UserProfile() {

  const [users, setUsers] =
  useState([]);

  useEffect(() => {

    fetch("/users")
      .then(res => res.json())
      .then(data => setUsers(data));

  }, []);

}
```

---

Component 2:

```jsx
function UserDashboard() {

  const [users, setUsers] =
  useState([]);

  useEffect(() => {

    fetch("/users")
      .then(res => res.json())
      .then(data => setUsers(data));

  }, []);

}
```

---

Notice:

```text
Same Logic
Repeated
```

---

Problem:

```text
Code Duplication
```

---

If API logic changes:

```text
Need To Update
Multiple Components
```

---

Not maintainable.

---

Need:

```text
Reusable Logic
```

---

React provides:

```text
Custom Hooks
```

---

# 2. What is a Custom Hook?

Definition:

> A Custom Hook is a JavaScript function that uses one or more React Hooks and allows reusable stateful logic across multiple components.

---

Simple Meaning:

```text
Reusable Hook Logic
```

---

Instead of:

```text
Copy-Paste Logic
```

---

Create:

```jsx
useUsers()
```

---

Reuse everywhere.

---

# 3. Why React Created Custom Hooks?

Before Hooks:

```text
Mixins

HOCs

Render Props
```

---

Used for logic sharing.

---

Problems:

```text
Complex

Nested Components

Hard To Understand
```

---

React Hooks introduced:

```text
Custom Hooks
```

---

Cleaner.

---

More reusable.

---

# 4. Real Life Example

Imagine:

```text
Washing Machine
```

---

You don't build:

```text
Motor

Drum

Electronics
```

every time.

---

You use:

```text
Reusable Machine
```

---

Similarly:

```text
Custom Hook
```

is reusable logic.

---

# 5. Important Clarification

Custom Hook:

```text
Is NOT A Special React Feature
```

---

It is simply:

```text
JavaScript Function
```

---

That uses:

```text
React Hooks
```

---

Example:

```jsx
function useCounter() {

}
```

---

Nothing magical.

---

# 6. Naming Rule

Must start with:

```text
use
```

---

Correct:

```jsx
useCounter()

useFetch()

useTheme()
```

---

Wrong:

```jsx
counter()

fetchData()
```

---

Reason:

React identifies hooks by:

```text
use Prefix
```

---

# First Custom Hook

---

# 7. Simple Counter Hook

```jsx
function useCounter() {

  const [count, setCount] =
  useState(0);

  const increment =
  () => setCount(c => c + 1);

  const decrement =
  () => setCount(c => c - 1);

  return {

    count,
    increment,
    decrement

  };

}
```

---

# Usage

```jsx
function App() {

  const {

    count,
    increment,
    decrement

  } = useCounter();

}
```

---

Output:

```text
Reusable Counter Logic
```

---

# Visualization

```text
App
 ↓

useCounter()

 ↓

count

increment()

decrement()
```

---

# Why Use Custom Hooks?

---

# 8. Code Reusability

Without Hook:

```text
Duplicate Logic
```

---

With Hook:

```text
Write Once

Reuse Everywhere
```

---

# 9. Better Separation

Component:

```text
UI
```

---

Hook:

```text
Business Logic
```

---

Cleaner architecture.

---

# Example

---

# 10. Without Custom Hook

```jsx
function UserList() {

  const [users,
  setUsers]
  =
  useState([]);

  useEffect(() => {

    fetch("/users")
    .then(res => res.json())
    .then(data => {

      setUsers(data);

    });

  }, []);

}
```

---

Repeated everywhere.

---

# 11. With Custom Hook

```jsx
function useUsers() {

  const [users,
  setUsers]
  =
  useState([]);

  useEffect(() => {

    fetch("/users")
    .then(res => res.json())
    .then(data => {

      setUsers(data);

    });

  }, []);

  return users;

}
```

---

Usage:

```jsx
const users =
useUsers();
```

---

Much cleaner.

---

# Real World Example

---

# 12. Online Status Hook

```jsx
function useOnlineStatus() {

  const [online,
  setOnline]
  =
  useState(
    navigator.onLine
  );

  useEffect(() => {

    const onlineHandler =
    () => setOnline(true);

    const offlineHandler =
    () => setOnline(false);

    window.addEventListener(
      "online",
      onlineHandler
    );

    window.addEventListener(
      "offline",
      offlineHandler
    );

    return () => {

      window.removeEventListener(
        "online",
        onlineHandler
      );

      window.removeEventListener(
        "offline",
        offlineHandler
      );

    };

  }, []);

  return online;

}
```

---

Usage:

```jsx
const isOnline =
useOnlineStatus();
```

---

Output:

```text
true

false
```

---

Reusable everywhere.

---

# Hook Composition

---

# 13. Hooks Can Use Hooks

Important concept.

---

Example:

```jsx
function useUser() {

  const [user,
  setUser]
  =
  useState(null);

}
```

---

Custom Hooks can use:

```text
useState

useEffect

useContext

useReducer

Other Custom Hooks
```

---

# Example

---

# 14. Hook Inside Hook

```jsx
function useAuth() {

  const isOnline =
  useOnlineStatus();

  return isOnline;

}
```

---

Custom hooks can compose:

```text
Other Hooks
```

---

Powerful pattern.

---

# Returning Values

---

# 15. Return Single Value

```jsx
function useTheme() {

  return "dark";

}
```

---

Usage:

```jsx
const theme =
useTheme();
```

---

# 16. Return Object

Most common.

---

```jsx
return {

  user,
  login,
  logout

};
```

---

Usage:

```jsx
const {

  user,
  login,
  logout

} = useAuth();
```

---

# 17. Return Array

Also common.

---

```jsx
return [

  count,

  increment

];
```

---

Usage:

```jsx
const [

 count,

 increment

]
=
useCounter();
```

---

# Custom Hook + useReducer

---

# 18. Example

```jsx
function useCounter() {

  const reducer =
  (state, action) => {

    switch(action.type) {

      case "INC":

        return state + 1;

      case "DEC":

        return state - 1;

      default:

        return state;

    }

  };

  return useReducer(
    reducer,
    0
  );

}
```

---

Usage:

```jsx
const [

 count,

 dispatch

]
=
useCounter();
```

---

# Custom Hook + Context

---

# 19. Example

```jsx
function useAuth() {

  return useContext(
    AuthContext
  );

}
```

---

Usage:

```jsx
const {

 user

}
=
useAuth();
```

---

Instead of:

```jsx
useContext(
 AuthContext
)
```

everywhere.

---

Cleaner.

---

# Custom Hook + useDebugValue

---

# 20. Best Practice

Example:

```jsx
function useOnlineStatus() {

  const online =
  true;

  useDebugValue(

   online
   ? "Online"
   : "Offline"

  );

  return online;

}
```

---

React DevTools:

```text
Online
```

---

Very useful.

---

# Custom Hook + API Calls

---

# 21. useFetch Example

```jsx
function useFetch(url) {

  const [data,
  setData]
  =
  useState(null);

  const [loading,
  setLoading]
  =
  useState(true);

  useEffect(() => {

    fetch(url)
    .then(res => res.json())
    .then(result => {

      setData(result);

      setLoading(false);

    });

  }, [url]);

  return {

    data,

    loading

  };

}
```

---

Usage:

```jsx
const {

 data,

 loading

}
=
useFetch(
 "/users"
);
```

---

Reusable API logic.

---

# Advanced Example

---

# 22. Debounce Hook

Very common interview topic.

---

Purpose:

```text
Delay User Input
```

---

Example:

```jsx
function useDebounce(

 value,

 delay

) {

 const [debounced,
 setDebounced]
 =
 useState(value);

 useEffect(() => {

  const timer =
  setTimeout(() => {

   setDebounced(
    value
   );

  }, delay);

  return () => {

   clearTimeout(
    timer
   );

  };

 }, [value, delay]);

 return debounced;

}
```

---

Usage:

```jsx
const debouncedSearch =
useDebounce(
 search,
 500
);
```

---

Used heavily in:

```text
Search Bars

Auto Complete

Filtering
```

---

# Internal Working

---

# 23. Does React Treat Custom Hooks Differently?

No.

---

React sees:

```jsx
useCounter()
```

---

And executes:

```jsx
useState()

useEffect()
```

inside it.

---

Nothing special.

---

# 24. Important Concept

Every component gets:

```text
Its Own Hook State
```

---

Example:

```jsx
<User />

<User />
```

---

Each receives:

```text
Separate State
```

---

Not shared automatically.

---

# Visualization

```text
User A

 useCounter()

 count = 0

----------------

User B

 useCounter()

 count = 0
```

---

Independent.

---

# Common Mistakes

---

# 25. Calling Hook Conditionally

Wrong:

```jsx
if(show) {

 useCounter();

}
```

---

Violates:

```text
Rules Of Hooks
```

---

# 26. Forgetting use Prefix

Wrong:

```jsx
function counter() {

 useState(0);

}
```

---

React cannot recognize it.

---

Correct:

```jsx
function useCounter() {

}
```

---

# 27. Returning Huge Objects

Bad:

```jsx
return {

  50 properties

};
```

---

Creates:

```text
Maintenance Problems
```

---

Expose only required data.

---

# 28. Mixing UI and Logic

Bad:

```jsx
function useCounter() {

 return (

  <button>

  </button>

 );

}
```

---

Hooks should return:

```text
Logic

State

Functions
```

---

Not JSX.

---

# Best Practices

---

# 29. Keep Hooks Focused

Good:

```text
useFetch()

useAuth()

useTheme()
```

---

Bad:

```text
useEverything()
```

---

# 30. Reuse Business Logic

Move:

```text
API Calls

Validation

Timers

Subscriptions
```

---

Into hooks.

---

# 31. Use useDebugValue

For library hooks.

---

Example:

```jsx
useDebugValue(
 "Logged In"
);
```

---

Helpful debugging.

---

# 32. Compose Small Hooks

Good:

```jsx
useAuth()

useTheme()

usePermissions()
```

---

Instead of:

```jsx
useMegaHook()
```

---

# Real World Hooks

---

# 33. Common Production Hooks

Examples:

```text
useAuth()

useFetch()

useApi()

useTheme()

useDebounce()

useLocalStorage()

useOnlineStatus()

usePermissions()

useMediaQuery()

usePagination()
```

---

# 34. useLocalStorage Example

```jsx
function useLocalStorage(

 key,

 initialValue

) {

 const [value,
 setValue]
 =
 useState(() => {

  const saved =
  localStorage.getItem(
   key
  );

  return saved
   ? JSON.parse(saved)
   : initialValue;

 });

 useEffect(() => {

  localStorage.setItem(

   key,

   JSON.stringify(
    value
   )

  );

 }, [key, value]);

 return [

  value,

  setValue

 ];

}
```

---

Usage:

```jsx
const [

 theme,

 setTheme

]
=
useLocalStorage(
 "theme",
 "light"
);
```

---

# Custom Hooks vs Components

---

# 35. Component

Returns:

```jsx
JSX
```

---

Example:

```jsx
function Button() {

 return (
  <button>
   Click
  </button>
 );

}
```

---

# 36. Custom Hook

Returns:

```text
State

Functions

Logic
```

---

Example:

```jsx
function useCounter() {

 return {

  count,

  increment

 };

}
```

---

# Comparison

| Feature | Component | Custom Hook |
|----------|------------|------------|
| Returns JSX | Yes | No |
| Uses Hooks | Yes | Yes |
| Reusable Logic | No | Yes |
| UI Rendering | Yes | No |
| State Sharing Logic | Limited | Excellent |

---

# React Internals

---

# 37. How React Executes Custom Hooks

Example:

```jsx
const data =
useFetch();
```

---

React expands:

```text
useFetch()

 ↓

useState()

useEffect()
```

---

Hook chain becomes:

```text
Component
 ↓

Custom Hook
 ↓

Built-in Hooks
```

---

# 38. React Doesn't Store Custom Hook Separately

React stores:

```text
Underlying Hooks
```

---

Such as:

```text
useState

useReducer

useEffect
```

---

Custom hook is:

```text
Abstraction Layer
```

---

# Interview Questions

---

### Q1. What is a Custom Hook?

```text
Reusable Logic
Built Using React Hooks
```

---

### Q2. Why Do We Need Custom Hooks?

```text
Avoid Code Duplication
```

---

### Q3. Must Custom Hooks Start With use?

```text
Yes
```

---

### Q4. Can Custom Hooks Use Other Hooks?

```text
Yes
```

---

### Q5. Can Custom Hooks Return JSX?

```text
No
```

---

Hooks return:

```text
Logic

State

Functions
```

---

### Q6. Do Custom Hooks Share State Automatically?

```text
No
```

---

Each component gets:

```text
Independent State
```

---

### Q7. Can Custom Hooks Use Context?

```text
Yes
```

---

Very common.

---

### Q8. Can Custom Hooks Use useReducer?

```text
Yes
```

---

Any React Hook.

---

### Q9. What Is The Biggest Benefit?

```text
Code Reusability
```

---

### Q10. What Is A Real World Example?

```text
useFetch()

useAuth()

useDebounce()

useLocalStorage()
```

---

# Memory Tricks

### Component

```text
Returns UI
```

---

### Hook

```text
Returns Logic
```

---

### Custom Hook

```text
Reusable Logic
```

---

### Naming Rule

```text
Must Start With

use
```

---

### Main Purpose

```text
Share Stateful Logic
```

---

### Rule

```text
Repeated Logic?
 ↓

Create Custom Hook
```

---

# Quick Revision

```text
Custom Hooks
------------

Purpose:

Reuse Stateful Logic

Naming:

Must Start With "use"

Examples:

✔ useFetch()

✔ useAuth()

✔ useTheme()

✔ useDebounce()

✔ useLocalStorage()

Can Use:

✔ useState

✔ useEffect

✔ useReducer

✔ useContext

✔ Other Custom Hooks

Advantages:

✔ Reusable Logic

✔ Cleaner Components

✔ Better Separation

✔ Easier Maintenance

Disadvantages:

✘ Can Be Over-Abstracted

✘ Hard To Debug If Too Complex

Remember:

Component
 ↓
Returns JSX

Custom Hook
 ↓
Returns Logic

Main Goal
 ↓
Code Reusability
```

---

# Final Hooks Roadmap

```text
Basic Hooks
-----------

useState
 ↓
State

useEffect
 ↓
Side Effects

useContext
 ↓
Global Data


Additional Hooks
----------------

useReducer
 ↓
Complex State

useRef
 ↓
References

useMemo
 ↓
Memoize Value

useCallback
 ↓
Memoize Function

useLayoutEffect
 ↓
Before Paint

useImperativeHandle
 ↓
Customize Ref

useDebugValue
 ↓
Debug Custom Hooks

useId
 ↓
Unique IDs


Advanced Hooks
--------------

Custom Hooks
 ↓
Reusable Logic
Built Using Hooks
```

---
