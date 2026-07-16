
## Definition

`@Lob` (Large Object) is a JPA annotation used to map large amounts of data to the database.

It tells JPA/Hibernate:

> Store this field as a Large Object (LOB) in the database.

Package:

```java
import jakarta.persistence.Lob;
```

Prior to Spring Boot 3:

```java
import javax.persistence.Lob;
```

---

# Why Do We Need @Lob?

Normal database columns have size limitations.

Examples:

```java
@Column(length = 255)
private String description;
```

---

Good for:

```text
Names
Emails
Addresses
```

---

Not suitable for:

```text
Images
Videos
PDF Files
Large Documents
Large Text Content
```

---

Solution:

```java
@Lob
```

---

# Types of LOB

There are two major types:

```text
1. CLOB (Character Large Object)
2. BLOB (Binary Large Object)
```

---

# CLOB

Used for:

```text
Large Text
HTML Content
Articles
Logs
Descriptions
JSON/XML Documents
```

---

Java Type:

```java
String
char[]
Character[]
```

---

Example:

```java
@Lob
private String description;
```

---

Database:

```sql
CLOB
```

or

```sql
TEXT
LONGTEXT
```

depending on database.

---

# BLOB

Used for:

```text
Images
PDF Files
Videos
Audio Files
Zip Files
Binary Data
```

---

Java Type:

```java
byte[]
Byte[]
```

---

Example:

```java
@Lob
private byte[] image;
```

---

Database:

```sql
BLOB
```

---

# Basic Syntax

## Text Data

```java
@Lob
private String content;
```

---

Meaning:

```text
Store large text
```

---

## Binary Data

```java
@Lob
private byte[] file;
```

---

Meaning:

```text
Store large binary data
```

---

# Example: Store Large Description

```java
@Entity
public class Product {

    @Id
    private Long id;

    private String name;

    @Lob
    private String description;
}
```

---

Generated SQL:

```sql
DESCRIPTION CLOB
```

---

Can store:

```text
Thousands or Millions of Characters
```

---

# Example: Store Image

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String name;

    @Lob
    private byte[] photo;
}
```

---

Generated SQL:

```sql
PHOTO BLOB
```

---

Stores:

```text
PNG
JPEG
GIF
```

---

# Example: Store PDF

```java
@Entity
public class Document {

    @Id
    private Long id;

    private String fileName;

    @Lob
    private byte[] pdfFile;
}
```

---

Database:

```sql
PDF_FILE BLOB
```

---

# Internal Mapping

```java
@Lob
private String content;
```

---

Hibernate:

```text
String
   ↓
CLOB
```

---

```java
@Lob
private byte[] image;
```

---

Hibernate:

```text
byte[]
   ↓
BLOB
```

---

# How Hibernate Determines CLOB vs BLOB

Based on Java Type.

---

String:

```java
@Lob
private String content;
```

---

Result:

```text
CLOB
```

---

byte[]:

```java
@Lob
private byte[] image;
```

---

Result:

```text
BLOB
```

---

# Fetching Large Objects

Example:

```java
Employee employee =
    employeeRepository
    .findById(1L)
    .get();
```

---

Hibernate loads:

```text
Photo
Description
LOB Data
```

---

Large files can impact performance.

---

# @Lob + @Basic(fetch = FetchType.LAZY)

Common optimization.

---

```java
@Lob

@Basic(
    fetch = FetchType.LAZY
)
private byte[] photo;
```

---

Meaning:

```text
Photo not loaded immediately.
```

---

Loaded only when accessed.

---

Useful for:

```text
Images
Videos
Large PDFs
```

---

# Example

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String name;

    @Lob

    @Basic(
        fetch = FetchType.LAZY
    )
    private byte[] image;
}
```

---

Benefit:

```text
Faster Queries
Less Memory Usage
```

---

# Database Mapping Examples

## MySQL

```java
@Lob
private String description;
```

---

May become:

```sql
LONGTEXT
```

---

```java
@Lob
private byte[] image;
```

---

May become:

```sql
LONGBLOB
```

---

# PostgreSQL

Text:

```sql
TEXT
```

---

Binary:

```sql
BYTEA
```

---

# Oracle

Text:

```sql
CLOB
```

---

Binary:

```sql
BLOB
```

---

# @Lob vs @Column(length)

Interview Question.

---

## @Column(length)

```java
@Column(length = 500)
private String description;
```

---

Limited size.

---

Suitable for:

```text
Small Strings
```

---

## @Lob

```java
@Lob
private String description;
```

---

Large text.

---

Suitable for:

```text
Huge Content
```

---

Comparison:

| Feature | @Column(length) | @Lob |
|----------|----------------|-------|
| Small Text | ✅ | ✅ |
| Large Text | ❌ | ✅ |
| Images | ❌ | ✅ |
| PDFs | ❌ | ✅ |

---

# @Lob vs File System Storage

Interview Question.

---

## Store In Database

```java
@Lob
private byte[] image;
```

---

Pros:

```text
Transactional
Easy Backup
Consistent
```

---

Cons:

```text
Large DB Size
Slower Queries
```

---

## Store In File System / Cloud

Store:

```text
S3
Azure Blob Storage
File Server
```

---

DB stores:

```text
File Path
URL
```

---

Preferred for:

```text
Large Images
Videos
Documents
```

---

# Internal Hibernate Processing

Example:

```java
@Lob
private byte[] photo;
```

---

Hibernate Metadata:

```text
LOB Type
Column Type
Fetch Strategy
```

---

During Save:

```java
employeeRepository.save(emp);
```

---

Hibernate:

```text
Converts Binary Data
Stores As BLOB
```

---

# Common Mistakes

## Mistake 1

Using @Lob For Small Strings

Wrong:

```java
@Lob
private String name;
```

---

Use:

```java
@Column(length = 100)
```

---

# Mistake 2

Loading Huge Files Eagerly

Wrong:

```java
@Lob
private byte[] image;
```

---

May load MBs of data.

---

Use:

```java
@Basic(fetch = FetchType.LAZY)
```

---

# Mistake 3

Storing Videos In DB

Possible but often inefficient.

---

Better:

```text
Azure Blob Storage
AWS S3
Google Cloud Storage
```

---

Store URL in DB.

---

# Common Interview Questions

## What is @Lob?

Used for storing large objects.

---

## What are the two LOB types?

```text
CLOB
BLOB
```

---

## What does String become?

```text
CLOB
```

---

## What does byte[] become?

```text
BLOB
```

---

## Can @Lob store images?

Yes.

---

## Can @Lob store PDFs?

Yes.

---

## Can @Lob store large text?

Yes.

---

## How do you optimize LOB loading?

```java
@Basic(fetch = FetchType.LAZY)
```

---

# Real-World Production Example

```java
@Entity
@Table(name = "documents")
public class Document {

    @Id

    @GeneratedValue
    private Long id;

    private String fileName;

    @Lob

    @Basic(
        fetch = FetchType.LAZY
    )
    private byte[] documentData;
}
```

---

Usage:

```text
Store PDF
Store Word File
Store Image
Store Report
```

---

# Key Points To Remember

- `@Lob` stands for Large Object.
- Used for storing large text and binary data.
- String → CLOB.
- byte[] → BLOB.
- Commonly used for images, PDFs, and documents.
- Can store huge text content.
- Often combined with `@Basic(fetch = FetchType.LAZY)`.
- Different databases map LOBs differently.
- Excessive use can impact performance.
- Frequently asked JPA and Hibernate interview topic.

---

# Quick Comparison

| Annotation | Purpose |
|------------|----------|
| `@Lob` | Large Objects |
| `@Column` | Normal Columns |
| `@Transient` | Ignore Field |
| `@Basic` | Fetch Strategy |
| `@Id` | Primary Key |

---

# Interview Shortcut

```java
@Lob

@Basic(fetch = FetchType.LAZY)
private byte[] image;
```

Meaning:

```text
Large Binary Data
        ↓
Stored As BLOB
        ↓
Loaded Lazily
        ↓
Better Performance
```

This is the most common enterprise usage of `@Lob` for storing documents, images, and other large data in JPA applications.