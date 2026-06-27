# Forms

> **Goal:** Understand React Forms from scratch, Controlled Components, Uncontrolled Components, Form Validation, Multiple Input Handling, Checkbox Handling, Radio Button Handling, Select Dropdowns, Form Submission, File Uploads, internal working, real-world examples, best practices, and interview questions.

---

# 1. What are Forms?

Forms allow users to:

```text
Enter Data

Update Data

Submit Data
```

---

Examples:

```text
Login Form

Registration Form

Search Form

Contact Form

Payment Form
```

---

Almost every React application contains forms.

---

# 2. Why Forms are Important?

Users interact with applications using forms.

---

Examples:

```text
Username

Password

Email

Address

Phone Number
```

---

Without forms:

```text
No User Input
```

---

# 3. React Form Flow

```text
User Input
      ↓

State Update
      ↓

Validation
      ↓

Submission
      ↓

API Request
```

---

# 4. Simple HTML Form

```html
<form>

 <input type="text" />

 <button>

  Submit

 </button>

</form>
```

---

React manages forms differently.

---

Using:

```text
State
```

---

# Controlled Components

---

# 5. What are Controlled Components?

Most important React form concept.

---

Definition:

> A Controlled Component is a form element whose value is controlled by React State.

---

Simple Meaning:

```text
React Controls Input Value
```

---

# 6. Flow of Controlled Component

```text
Input Value
      ↓

React State
      ↓

UI Update
```

---

React becomes:

```text
Single Source Of Truth
```

---

# 7. Controlled Input Example

```jsx
import { useState } from "react";

function App() {

 const [

  name,

  setName

 ] = useState("");

 return (

  <input

   value={name}

   onChange={(e) =>

    setName(

     e.target.value

    )
   }

  />

 );
}
```

---

# 8. Internal Working

User types:

```text
J
```

---

React executes:

```jsx
setName("J");
```

---

State becomes:

```text
J
```

---

React re-renders.

---

Input value updates.

---

# 9. Why Controlled Components?

Benefits:

```text
Validation

State Management

Real-Time Updates

Predictable Behavior
```

---

# 10. Controlled Textarea

```jsx
<textarea

 value={message}

 onChange={(e) =>

  setMessage(

   e.target.value

  )
 }

/>
```

---

Works same as input.

---

# 11. Controlled Select

```jsx
<select

 value={country}

 onChange={(e) =>

  setCountry(

   e.target.value

  )
 }
>
```

---

React controls selected value.

---

# Uncontrolled Components

---

# 12. What are Uncontrolled Components?

Definition:

> Form elements whose state is managed by the DOM instead of React.

---

Simple Meaning:

```text
DOM Controls Input
```

---

# 13. Example

```jsx
<input type="text" />
```

---

React does not track value.

---

Browser manages it.

---

# 14. Accessing Uncontrolled Values

Using:

```text
useRef
```

---

# 15. Example

```jsx
import { useRef } from "react";

function App() {

 const inputRef =

 useRef();

 function handleSubmit() {

  console.log(

   inputRef.current.value

  );
 }

 return (

  <>

   <input

    ref={inputRef}

   />

   <button

    onClick={handleSubmit}

   >

    Submit

   </button>

  </>

 );
}
```

---

# 16. Controlled vs Uncontrolled

| Feature | Controlled | Uncontrolled |
|----------|----------|----------|
| Managed By | React State | DOM |
| Validation | Easy | Hard |
| Real-Time Updates | Yes | No |
| Recommended | Yes | Sometimes |

---

# 17. Which One Should You Use?

Most React applications use:

```text
Controlled Components
```

---

Because:

```text
Better Control

Better Validation

Predictable Behavior
```

---

# Form Validation

---

# 18. What is Form Validation?

Checking user input before submission.

---

Examples:

```text
Required Field

Email Format

Password Length

Phone Validation
```

---

# 19. Why Validation?

Prevent:

```text
Invalid Data
```

---

Entering database.

---

# 20. Required Field Validation

```jsx
if(name === "") {

 alert(

  "Name Required"

 );
}
```

---

# 21. Email Validation

```jsx
const emailRegex =

 /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
```

---

Check:

```jsx
if(

 !emailRegex.test(email)

) {

 alert(

  "Invalid Email"

 );
}
```

---

# 22. Password Validation

```jsx
if(

 password.length < 8

) {

 alert(

  "Minimum 8 Characters"

 );
}
```

---

# 23. Real-Time Validation

```jsx
<input

 value={email}

 onChange={(e) => {

  setEmail(

   e.target.value

  );

  validateEmail(
   e.target.value
  );

 }}

/>
```

---

Validation while typing.

---

# Multiple Input Handling

---

# 24. Problem

Form contains:

```text
Name

Email

Phone

Address
```

---

Managing separate handlers:

```text
Too Much Code
```

---

# 25. Single State Object

```jsx
const [

 form,

 setForm

]
=
useState({

 name: "",

 email: ""
});
```

---

# 26. Generic Handler

```jsx
function handleChange(

 event

) {

 const {

  name,

  value

 } = event.target;

 setForm({

  ...form,

  [name]: value
 });
}
```

---

# 27. Inputs

```jsx
<input

 name="name"

 onChange={handleChange}

/>
```

---

```jsx
<input

 name="email"

 onChange={handleChange}

/>
```

---

One handler manages everything.

---

# 28. Internal Working

Input:

```text
name
```

changes.

---

React updates:

```jsx
form.name
```

---

Input:

```text
email
```

changes.

---

React updates:

```jsx
form.email
```

---

Very common interview topic.

---

# Checkbox Handling

---

# 29. Checkbox Basics

Checkbox represents:

```text
True

False
```

---

# 30. Example

```jsx
<input

 type="checkbox"

/>
```

---

# 31. Controlled Checkbox

```jsx
const [

 isChecked,

 setIsChecked

]
=
useState(false);
```

---

```jsx
<input

 type="checkbox"

 checked={isChecked}

 onChange={(e) =>

  setIsChecked(

   e.target.checked

  )
 }

/>
```

---

# 32. Why checked?

Checkbox uses:

```text
checked
```

instead of:

```text
value
```

---

# 33. Terms & Conditions Example

```jsx
{

 isChecked

 &&

 <button>

  Submit

 </button>

}
```

---

Button appears only when checked.

---

# Radio Button Handling

---

# 34. What are Radio Buttons?

Allow user to select:

```text
Only One Option
```

---

Example:

```text
Male

Female

Other
```

---

# 35. Example

```jsx
const [

 gender,

 setGender

]
=
useState("");
```

---

```jsx
<input

 type="radio"

 value="Male"

 checked={

  gender === "Male"

 }

 onChange={(e) =>

  setGender(

   e.target.value

  )
 }

/>
```

---

# 36. Multiple Radios

```jsx
<input

 type="radio"

 value="Male"
/>
```

---

```jsx
<input

 type="radio"

 value="Female"
/>
```

---

Only one selected.

---

# Select Dropdown

---

# 37. What is Select Dropdown?

Used when user chooses:

```text
One Option
From Many
```

---

# 38. Example

```jsx
<select>

 <option>

  India

 </option>

</select>
```

---

# 39. Controlled Dropdown

```jsx
const [

 country,

 setCountry

]
=
useState("");
```

---

```jsx
<select

 value={country}

 onChange={(e) =>

  setCountry(

   e.target.value

  )
 }
>

 <option>

  India

 </option>

 <option>

  USA

 </option>

</select>
```

---

# 40. Dynamic Dropdown

```jsx
countries.map(country => (

 <option

  key={country.id}

 >

  {country.name}

 </option>

))
```

---

Very common.

---

# Form Submission

---

# 41. What is Form Submission?

Sending form data.

---

Example:

```text
Login Request

Registration Request

Search Request
```

---

# 42. Submit Event

```jsx
<form

 onSubmit={handleSubmit}

>
```

---

# 43. Example

```jsx
function handleSubmit(

 event

) {

 event.preventDefault();

 console.log(form);
}
```

---

# 44. Why preventDefault()?

Without it:

```text
Browser Reloads Page
```

---

React applications usually avoid:

```text
Page Refresh
```

---

# 45. Full Example

```jsx
<form

 onSubmit={handleSubmit}

>

 <input

  value={name}

  onChange={(e) =>

   setName(

    e.target.value

   )
  }

 />

 <button>

  Submit

 </button>

</form>
```

---

# 46. API Submission Example

```jsx
async function handleSubmit() {

 await axios.post(

  "/users",

  form

 );
}
```

---

Very common.

---

# File Upload

---

# 47. What is File Upload?

Allows users to upload:

```text
Images

PDFs

Videos

Documents
```

---

# 48. File Input

```jsx
<input

 type="file"

/>
```

---

# 49. Getting Selected File

```jsx
function handleFile(

 event

) {

 console.log(

  event.target.files[0]

 );
}
```

---

# 50. Example

```jsx
<input

 type="file"

 onChange={handleFile}

/>
```

---

# 51. Store File in State

```jsx
const [

 file,

 setFile

]
=
useState(null);
```

---

```jsx
setFile(

 event.target.files[0]

);
```

---

# 52. Upload File Using FormData

```jsx
const formData =

 new FormData();
```

---

```jsx
formData.append(

 "file",

 file

);
```

---

# 53. Sending File

```jsx
await axios.post(

 "/upload",

 formData
);
```

---

Very common interview topic.

---

# 54. Multiple File Upload

```jsx
<input

 type="file"

 multiple

/>
```

---

Allows:

```text
Multiple Files
```

---

# Best Practices

---

# 55. Prefer Controlled Components

Recommended.

---

Provides:

```text
Better Validation

Better State Control
```

---

# 56. Validate Before Submit

Check:

```text
Required Fields

Email

Password

Phone
```

---

Before API call.

---

# 57. Use Generic Input Handler

Avoid:

```text
Separate Handlers
For Every Field
```

---

# 58. Use FormData For Files

Required for:

```text
Multipart Upload
```

---

# 59. Disable Submit During Loading

Example:

```jsx
<button

 disabled={loading}

>

 Submit

</button>
```

---

Improves UX.

---

# Interview Questions

---

### Q1. What is a Controlled Component?

```text
Input controlled by
React State.
```

---

### Q2. What is an Uncontrolled Component?

```text
Input controlled by
DOM.
```

---

### Q3. Which is preferred?

```text
Controlled Component
```

---

### Q4. Why use Controlled Components?

```text
Validation

Predictable State

Better Control
```

---

### Q5. How do you handle multiple inputs?

```jsx
name attribute

+

Single Change Handler
```

---

### Q6. How do you handle checkboxes?

```jsx
checked

onChange
```

---

### Q7. How do you handle radio buttons?

```jsx
Store Selected Value
In State
```

---

### Q8. How do you prevent page refresh?

```jsx
event.preventDefault()
```

---

### Q9. How do you upload files?

```jsx
input type="file"

+

FormData
```

---

### Q10. What is FormData?

```text
JavaScript object
used to send files
and form data.
```

---

# Real World Example

### Registration Form

Fields:

```text
Name

Email

Password

Gender

Country

Profile Picture
```

---

Features:

```text
Controlled Inputs

Validation

File Upload

API Submission
```

---

Everything learned in this topic.

---

# Memory Tricks

### Controlled Component

```text
React Controls Input
```

---

### Uncontrolled Component

```text
DOM Controls Input
```

---

### Validation

```text
Check Before Submit
```

---

### Checkbox

```text
checked Property
```

---

### Radio Button

```text
One Selection Only
```

---

### Select

```text
Choose One Option
```

---

### Form Submission

```text
preventDefault()
```

---

### File Upload

```text
FormData
```

---

# Quick Revision

```text
Forms
-----

Controlled Component
--------------------

React State
Controls Input

Uncontrolled Component
----------------------

DOM Controls Input

Validation
----------

Required Fields

Email Check

Password Check

Multiple Inputs
---------------

Single State Object

Generic Handler

Checkbox
--------

checked

Radio Button
------------

Single Selection

Dropdown
--------

select

option

Form Submission
---------------

onSubmit

preventDefault()

File Upload
-----------

input type="file"

FormData

Best Practice
-------------

Use Controlled Components

Validate Before Submit

Use FormData For Files
```