# Chapter 18 — MariaDB

## Introduction

Imagine someone visits your website.

They log in.

Read blog posts.

Write comments.

Upload images.

Where is all this information stored?

The answer is:

**MariaDB.**

Without MariaDB,

WordPress has no memory.

Every page would be empty.

Every user would disappear after restarting the website.

MariaDB is the brain that stores all of your website's data.

---

# What Is MariaDB?

MariaDB is a **Relational Database Management System (RDBMS).**

Let's simplify that.

Think of MariaDB as a huge digital filing cabinet.

Inside it, information is organized neatly so it can be stored, searched, updated, and deleted.

---

# Think About Excel

Imagine an Excel spreadsheet.

```
+----+----------+----------------+
| ID | Username | Email          |
+----+----------+----------------+
| 1  | admin    | admin@test.com |
| 2  | ali      | ali@test.com   |
+----+----------+----------------+
```

MariaDB stores data in a very similar way.

Rows and columns.

The difference is that MariaDB is much faster and can handle millions of rows.

---

# Database

A database is a collection of related information.

Example.

```
MariaDB Server

↓

wordpress
```

Your project contains one database called:

```
wordpress
```

Remember?

```sql
SHOW DATABASES;
```

Output:

```
information_schema

mysql

performance_schema

sys

wordpress
```

The one WordPress uses is:

```
wordpress
```

---

# Table

Inside a database,

there are tables.

Think of a database as a folder.

Think of tables as files inside that folder.

Example.

```
wordpress

├── wp_users

├── wp_posts

├── wp_comments

├── wp_options

└── wp_usermeta
```

Each table stores one type of information.

---

# Row

A row represents one record.

Example.

Table:

```
wp_users
```

```
+----+----------+----------------+
| ID | Username | Email          |
+----+----------+----------------+
| 1  | admin    | admin@test.com |
+----+----------+----------------+
```

This entire line is one row.

One user.

---

# Column

Columns describe the data.

```
ID

Username

Email
```

Each column stores one piece of information.

---

# Visual Example

```
wp_users

+----+----------+----------------+
| ID | Username | Email          |
+----+----------+----------------+
| 1  | admin    | admin@test.com |
| 2  | ali      | ali@test.com   |
+----+----------+----------------+
```

Columns

↓

```
ID

Username

Email
```

Rows

↓

```
admin

ali
```

---

# How Does WordPress Use MariaDB?

Imagine you open:

```
https://mowaidat.42.fr
```

WordPress asks:

```
Give me the latest blog posts.
```

MariaDB searches its tables.

Then returns:

```
Post 1

Post 2

Post 3
```

WordPress converts them into HTML.

The browser displays them.

---

# Where Are Users Stored?

Inside:

```
wp_users
```

Remember?

You created a user with:

```bash
wp user create
```

Where did that user go?

Into:

```
wp_users
```

You can even see it.

```sql
SELECT * FROM wp_users;
```

---

# Where Are Blog Posts Stored?

Inside:

```
wp_posts
```

Create a new blog post.

Then run:

```sql
SELECT * FROM wp_posts;
```

You'll find it.

---

# Where Are Comments Stored?

Inside:

```
wp_comments
```

Every comment becomes a new row.

---

# Does MariaDB Know WordPress?

No.

MariaDB has no idea what WordPress is.

It only understands SQL.

WordPress translates requests into SQL.

Example.

WordPress says:

```
Show the latest posts.
```

Internally,

it sends something similar to:

```sql
SELECT * FROM wp_posts
ORDER BY post_date DESC;
```

MariaDB executes the query.

Returns the result.

Done.

---

# Passwords

When you created the administrator account,

did MariaDB store:

```
123456
```

No.

Passwords are stored as **hashes**.

Example.

Instead of:

```
123456
```

MariaDB stores something like:

```
$P$B4hZ8...
```

This makes passwords much more secure.

---

# Why Don't We Store Everything In Files?

Imagine storing every user inside a text file.

To find one user,

you would read the entire file.

MariaDB can search millions of rows almost instantly.

It is built for this job.

---

# Your Project

Your MariaDB container has one job.

Store data.

It does not:

- execute PHP
- serve web pages
- receive browser requests

It simply stores and retrieves information.

---

# Connection

The connection looks like this.

```
Browser

↓

NGINX

↓

PHP-FPM

↓

WordPress

↓

MariaDB

↓

SQL

↓

MariaDB

↓

WordPress

↓

Browser
```

Notice.

The browser never talks directly to MariaDB.

Only WordPress does.

---

# Useful Commands

List databases.

```sql
SHOW DATABASES;
```

Use a database.

```sql
USE wordpress;
```

List tables.

```sql
SHOW TABLES;
```

View users.

```sql
SELECT * FROM wp_users;
```

View posts.

```sql
SELECT * FROM wp_posts;
```

View comments.

```sql
SELECT * FROM wp_comments;
```

---

# Key Points

MariaDB:

- is a relational database
- stores WordPress data
- organizes data into tables
- communicates using SQL
- does not know what WordPress is
- simply executes SQL queries

---

## Chapter Summary

After reading this chapter, you should be able to answer:

- What is MariaDB?
- What is a database?
- What is a table?
- What is a row?
- What is a column?
- Where are WordPress users stored?
- Where are posts stored?
- Why doesn't the browser connect directly to MariaDB?
