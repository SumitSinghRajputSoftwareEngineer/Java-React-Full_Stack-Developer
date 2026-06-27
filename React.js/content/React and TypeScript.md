# React and TypeScript

> **Goal:** Master React with TypeScript completely — understand TypeScript fundamentals, typing props, typing state, typing hooks, interfaces, types, generic components, real-world examples, interview questions, best practices, and how TypeScript improves React development.

---

# 1. What is TypeScript?

TypeScript is:

```text
JavaScript + Static Typing
```

---

Created by:

:contentReference[oaicite:0]{index=0}

---

TypeScript adds:

```text
Type Safety

Auto Completion

Better Refactoring

Compile-Time Error Checking
```

---

# JavaScript Problem

Example:

```javascript
function add(a, b) {

 return a + b;

}
```

---

Call:

```javascript
add("10", 20);
```

---

Output:

```text
1020
```

---

No error.

---

JavaScript allows this.

---

Can cause bugs.

---

# TypeScript Solution

```typescript
function add(
 a: number,
 b: number
): number {

 return a + b;

}
```

---

Now:

```typescript
add("10", 20);
```

---

Error:

```text
Argument of type string
is not assignable
to number
```

---

Bug prevented.

---

# Why Use TypeScript in React?

Large React applications have:

```text
Hundreds of Components

Many Props

Complex State

API Calls

Custom Hooks
```

---

Without typing:

```text
Runtime Errors
```

---

With TypeScript:

```text
Compile-Time Errors
```

---

Much safer.

---

# Benefits

✔ Type Safety

✔ Better IntelliSense

✔ Easier Refactoring

✔ Better Documentation

✔ Fewer Runtime Bugs

✔ Enterprise Standard

---

# TypeScript Basics

---

# 2. Basic Types

---

## String

```typescript
let name: string =
"John";
```

---

## Number

```typescript
let age: number = 25;
```

---

## Boolean

```typescript
let isAdmin: boolean =
true;
```

---

## Array

```typescript
let users: string[] =
["John", "Jane"];
```

---

Alternative:

```typescript
let users:
Array<string>;
```

---

## Object

```typescript
let user: {

 name: string;

 age: number;

};
```

---

Example:

```typescript
let user = {

 name: "John",

 age: 25

};
```

---

# Function Typing

```typescript
function greet(
 name: string
): string {

 return
 `Hello ${name}`;

}
```

---

Return type:

```typescript
string
```

---

# Void Functions

```typescript
function log(
 message: string
): void {

 console.log(message);

}
```

---

Returns:

```text
Nothing
```

---

# Any Type

```typescript
let value: any;
```

---

Can hold:

```text
String

Number

Boolean

Object
```

---

Avoid in production.

---

# Unknown Type

Safer than:

```typescript
any
```

---

Example:

```typescript
let data:
unknown;
```

---

Must validate before use.

---

# React with TypeScript

---

# 3. React Component Typing

Normal React:

```jsx
function Welcome() {

 return <h1>Hello</h1>;

}
```

---

TypeScript:

```tsx
function Welcome():
JSX.Element {

 return <h1>Hello</h1>;

}
```

---

Return type:

```typescript
JSX.Element
```

---

# Why TSX?

TypeScript + JSX

---

File extension:

```text
.tsx
```

---

instead of:

```text
.jsx
```

---

# Typing Props

---

# 4. What Are Typed Props?

Props should have:

```text
Expected Types
```

---

# Without TypeScript

```jsx
<UserCard
 age="abc"
/>
```

---

May cause bugs.

---

# With TypeScript

Errors appear immediately.

---

# Using Interface

```typescript
interface UserProps {

 name: string;

 age: number;

}
```

---

Use:

```tsx
function UserCard(
 props: UserProps
) {

 return (

  <h1>

   {props.name}

  </h1>

 );

}
```

---

Usage:

```tsx
<UserCard

 name="John"

 age={25}

/>
```

---

Valid.

---

# Invalid Usage

```tsx
<UserCard

 name="John"

 age="25"

/>
```

---

Error:

```text
Type string
not assignable
to number
```

---

# Props Destructuring

```tsx
interface UserProps {

 name: string;

 age: number;

}
```

---

```tsx
function UserCard({

 name,

 age

}: UserProps) {

 return (

  <h1>

   {name}

  </h1>

 );

}
```

---

Cleaner.

---

# Optional Props

Use:

```typescript
?
```

---

Example:

```typescript
interface UserProps {

 name: string;

 age?: number;

}
```

---

Optional:

```typescript
age
```

---

Usage:

```tsx
<UserCard
 name="John"
/>
```

---

Valid.

---

# Default Values

```tsx
function UserCard({

 age = 18

}: UserProps) {

}
```

---

Default applied.

---

# Typing State

---

# 5. Typing useState

Most common interview topic.

---

# String State

```tsx
const [

 name,

 setName

]
=
useState<string>(
 ""
);
```

---

Type:

```text
string
```

---

# Number State

```tsx
const [

 count,

 setCount

]
=
useState<number>(
 0
);
```

---

# Boolean State

```tsx
const [

 loading,

 setLoading

]
=
useState<boolean>(
 false
);
```

---

# Object State

```typescript
interface User {

 id: number;

 name: string;

}
```

---

```tsx
const [

 user,

 setUser

]
=
useState<User>(

 {
  id:1,

  name:"John"
 }

);
```

---

# Nullable State

Very common.

---

Example:

```tsx
const [

 user,

 setUser

]
=
useState<User | null>(
 null
);
```

---

Allows:

```typescript
User

OR

null
```

---

# Array State

```typescript
const [

 users,

 setUsers

]
=
useState<User[]>([]);
```

---

Array of users.

---

# Typing Hooks

---

# 6. Typing React Hooks

---

# useRef

Input example:

```tsx
const inputRef =

useRef<
HTMLInputElement
>(null);
```

---

Access:

```tsx
inputRef.current?.focus();
```

---

Type-safe.

---

# useReducer

State:

```typescript
interface State {

 count: number;

}
```

---

Action:

```typescript
type Action =

 | {
     type:
     "increment";
   }

 | {
     type:
     "decrement";
   };
```

---

Reducer:

```typescript
function reducer(

 state: State,

 action: Action

): State {

 switch(
  action.type
 ) {

  case "increment":

   return {

    count:
    state.count + 1

   };

  default:

   return state;

 }

}
```

---

Fully typed.

---

# useContext

Context type:

```typescript
interface ThemeContextType {

 theme: string;

 toggleTheme:
 () => void;

}
```

---

Create:

```tsx
const ThemeContext =

createContext<
ThemeContextType
 | null
>(null);
```

---

Safe usage.

---

# Interfaces

---

# 7. What is an Interface?

Interface defines:

```text
Shape Of Object
```

---

Example:

```typescript
interface User {

 id: number;

 name: string;

 email: string;

}
```

---

Object:

```typescript
const user: User = {

 id:1,

 name:"John",

 email:"john@gmail.com"

};
```

---

Must match interface.

---

# Benefits

✔ Reusable

✔ Readable

✔ Strong Typing

✔ Easy Maintenance

---

# Interface Extension

```typescript
interface Person {

 name: string;

}
```

---

Extend:

```typescript
interface Employee
extends Person {

 salary: number;

}
```

---

Result:

```typescript
name

salary
```

---

Available.

---

# Types

---

# 8. What is a Type?

Alternative to interface.

---

Example:

```typescript
type User = {

 id: number;

 name: string;

};
```

---

Works similarly.

---

# Type Alias

```typescript
type ID =
string | number;
```

---

Usage:

```typescript
let id: ID;
```

---

Can be:

```text
String

OR

Number
```

---

# Union Types

```typescript
type Status =

 | "loading"

 | "success"

 | "error";
```

---

Usage:

```typescript
let state: Status;
```

---

Only these values allowed.

---

# Interface vs Type

Most asked interview question.

---

Interface:

```typescript
interface User {

 name: string;

}
```

---

Type:

```typescript
type User = {

 name: string;

};
```

---

# Differences

| Feature | Interface | Type |
|----------|----------|----------|
| Object Definition | Yes | Yes |
| Extend | Yes | Yes |
| Union Types | No | Yes |
| Primitive Aliases | No | Yes |
| Declaration Merging | Yes | No |

---

# Rule of Thumb

Use:

```typescript
interface
```

for objects.

---

Use:

```typescript
type
```

for unions and aliases.

---

# Generic Components

---

# 9. What are Generics?

Definition:

> Generics allow components, functions, and hooks to work with multiple types safely.

---

Without Generics

Need:

```typescript
UserList

ProductList

OrderList
```

---

Different implementations.

---

Generics allow:

```text
One Reusable Component
```

---

# Generic Function

```typescript
function identity<T>(
 value: T
): T {

 return value;

}
```

---

Use:

```typescript
identity<string>(
 "Hello"
);
```

---

Or:

```typescript
identity<number>(
 10
);
```

---

Same function.

---

# Generic Component Example

Interface:

```typescript
interface ListProps<T> {

 items: T[];

 renderItem:
 (
  item: T
 ) => JSX.Element;

}
```

---

Component:

```tsx
function List<T>({

 items,

 renderItem

}: ListProps<T>) {

 return (

  <ul>

   {
    items.map(
     renderItem
    )
   }

  </ul>

 );

}
```

---

Usage:

```tsx
<List

 items={[
  "React",
  "Angular"
 ]}

 renderItem={
  item =>
  <li>{item}</li>
 }

/>
```

---

Type inferred automatically.

---

# Generic API Response

Very common.

---

```typescript
interface ApiResponse<T> {

 data: T;

 success: boolean;

}
```

---

User response:

```typescript
ApiResponse<User>
```

---

Product response:

```typescript
ApiResponse<Product>
```

---

Reusable.

---

# Real World Example

---

# User Interface

```typescript
interface User {

 id: number;

 name: string;

 email: string;

}
```

---

State:

```tsx
const [

 users,

 setUsers

]
=
useState<User[]>([]);
```

---

API Response:

```typescript
interface ApiResponse<T> {

 data: T;

 success: boolean;

}
```

---

Fetch:

```typescript
const response:

ApiResponse<User[]>;
```

---

Everything typed.

---

# Common Mistakes

---

# Using any Everywhere

Wrong:

```typescript
const user: any;
```

---

Removes type safety.

---

# Not Typing useState

Wrong:

```tsx
const [

 users,

 setUsers

]
=
useState([]);
```

---

Type becomes:

```text
never[]
```

sometimes.

---

Explicit typing preferred.

---

# Using Type for Everything

Not ideal.

---

Prefer:

```typescript
interface
```

for objects.

---

# Ignoring Null Values

Wrong:

```typescript
user.name
```

---

When:

```typescript
user = null
```

---

Can crash.

---

Use:

```typescript
user?.name
```

---

# Best Practices

---

# 10. Production Recommendations

✔ Use strict TypeScript mode

✔ Type all props

✔ Type all state

✔ Type API responses

✔ Prefer interfaces for objects

✔ Use generics for reusable components

✔ Avoid any

✔ Use union types

✔ Use nullable types properly

---

# Interview Questions

---

### Q1. What is TypeScript?

```text
JavaScript With Static Typing
```

---

### Q2. Why use TypeScript with React?

```text
Type Safety

Better Maintainability

Compile-Time Error Detection
```

---

### Q3. How do you type props?

```typescript
interface Props {

 name: string;

}
```

---

### Q4. How do you type useState?

```typescript
useState<string>("")
```

---

### Q5. How do you type arrays?

```typescript
User[]
```

---

### Q6. Interface vs Type?

Interface:

```text
Objects
```

---

Type:

```text
Aliases + Unions
```

---

### Q7. What are Generics?

```text
Reusable Type-Safe Logic
```

---

### Q8. How do you type useRef?

```typescript
useRef<HTMLInputElement>(
 null
)
```

---

### Q9. Why avoid any?

```text
Removes Type Safety
```

---

### Q10. What is a Union Type?

```typescript
string | number
```

---

Multiple allowed types.

---

# Memory Tricks

### TypeScript

```text
JavaScript + Types
```

---

### Interface

```text
Object Shape
```

---

### Type

```text
Alias
```

---

### useState<T>

```text
Typed State
```

---

### useRef<T>

```text
Typed DOM Reference
```

---

### Generic

```text
Reusable Type
```

---

### Union

```text
Multiple Types
```

---

### Nullable

```text
Type | null
```

---

# Complete TypeScript Flow

```text
Component
      ↓

Props Interface
      ↓

Typed State
      ↓

Typed Hooks
      ↓

API Response Types
      ↓

Generics
      ↓

Compile-Time Validation
      ↓

Safer React Application
```

---

# Quick Revision

```text
React + TypeScript
------------------

TypeScript
 ↓
 Static Typing

Props
 ↓
 Interface

State
 ↓
 useState<T>()

Hooks
 ↓
 Typed Hooks

Interface
 ↓
 Object Shape

Type
 ↓
 Alias

Generics
 ↓
 Reusable Types

Benefits:

✔ Type Safety

✔ Better IntelliSense

✔ Easier Refactoring

✔ Fewer Bugs

Remember:

Interface
 ↓
 Object Contract

Type
 ↓
 Alias / Union

Generic
 ↓
 Reusable Type

TypeScript
 ↓
 Catch Errors Before Runtime
```

---