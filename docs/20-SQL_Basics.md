# Chapter 20 — SQL Basics

## Introduction

Throughout this guide, we have mentioned SQL many times.

WordPress uses SQL to communicate with MariaDB.

MariaDB does not understand PHP.

It does not understand WordPress.

It only understands one language.

```
SQL
```

SQL stands for:

```
Structured Query Language
```

Think of SQL as the language you use to talk to a database.

---

# SQL Is Like Asking Questions

Imagine a library.

You ask:

```
Show me all books.
```

Or:

```
Show me books written by Mohammad.
```

The librarian searches and gives you the results.

MariaDB works exactly the same way.

WordPress asks questions using SQL.

MariaDB answers them.

---

# SHOW DATABASES

```sql
SHOW DATABASES;
```

This command asks:

```
What databases exist?
```

Example:

```
MariaDB

├── mysql
├── performance_schema
├── sys
└── wordpress
```

---

# USE

```sql
USE wordpress;
```

Imagine you have many databases.

Which one should SQL commands use?

This command selects one database.

After this:

```
wordpress
```

becomes your active database.

---

# SHOW TABLES

```sql
SHOW TABLES;
```

Output:

```
wp_users

wp_posts

wp_comments

wp_options

...
```

Every table stores one type of data.

---

# SELECT

Probably the most common SQL command.

```sql
SELECT * FROM wp_users;
```

Let's read it word by word.

---

## SELECT

Means:

```
I want to read data.
```

---

## *

Means:

```
Every column.
```

Instead of:

```
ID

Username

Email

Password
```

just write:

```
*
```

---

## FROM

Means:

```
Read from this table.
```

---

## wp_users

The table we want to read.

---

Whole sentence:

```sql
SELECT * FROM wp_users;
```

Meaning:

```
Read every column

from

every row

inside wp_users.
```

---

# Example Output

```
+----+-----------+----------------------+
| ID | user_login| user_email           |
+----+-----------+----------------------+
| 1  | admin     | admin@test.com       |
| 2  | ali       | ali@test.com         |
+----+-----------+----------------------+
```

Each row is one user.

---

# WHERE

Suppose you only want:

```
admin
```

Instead of everyone.

```sql
SELECT *

FROM wp_users

WHERE user_login='admin';
```

MariaDB searches only for:

```
admin
```

---

# INSERT

Suppose WordPress creates a new user.

Internally,

something similar happens.

```sql
INSERT INTO wp_users

(user_login,user_email)

VALUES

('ahmad','ahmad@test.com');
```

A new row is added.

---

Before

```
admin
ali
```

After

```
admin
ali
ahmad
```

---

# UPDATE

Suppose the email changes.

```sql
UPDATE wp_users

SET user_email='new@test.com'

WHERE ID=2;
```

MariaDB updates the existing row.

---

# DELETE

Suppose a user is removed.

```sql
DELETE FROM wp_users

WHERE ID=2;
```

The row disappears.

---

# CREATE DATABASE

You've already used this.

```sql
CREATE DATABASE wordpress;
```

Creates a new database.

---

# CREATE USER

```sql
CREATE USER 'mowaidat'@'%'

IDENTIFIED BY 'password';
```

Creates a MariaDB user.

Remember.

This is **not** a WordPress user.

This is a database user.

WordPress uses this account to connect.

---

# GRANT

```sql
GRANT ALL PRIVILEGES

ON wordpress.*

TO 'mowaidat'@'%';
```

Allows this database user to:

- Read data
- Insert data
- Update data
- Delete data

Without these permissions,

WordPress cannot work.

---

# Real Example

Imagine you open:

```
https://mowaidat.42.fr
```

WordPress wants the latest posts.

Internally,

it may execute something similar to:

```sql
SELECT *

FROM wp_posts

ORDER BY post_date DESC

LIMIT 10;
```

MariaDB returns:

```
10 rows
```

WordPress converts them into HTML.

---

# SQL Flow

```
WordPress

↓

SQL Query

↓

MariaDB

↓

Search Tables

↓

Return Rows

↓

WordPress
```

---

# SQL Does Not Know WordPress

MariaDB does not know what:

- Blog
- User
- Comment

means.

It only knows:

```
Tables

Rows

Columns

SQL
```

Everything else is handled by WordPress.

---

# Useful Commands

List databases.

```sql
SHOW DATABASES;
```

Choose a database.

```sql
USE wordpress;
```

List tables.

```sql
SHOW TABLES;
```

Read users.

```sql
SELECT * FROM wp_users;
```

Read posts.

```sql
SELECT * FROM wp_posts;
```

Read comments.

```sql
SELECT * FROM wp_comments;
```

---

# Key Points

SQL:

- is the language of MariaDB
- reads data
- inserts data
- updates data
- deletes data
- creates databases
- creates users

WordPress uses SQL to communicate with MariaDB.

MariaDB executes SQL and returns the results.

---

## Chapter Summary

After reading this chapter, you should be able to answer:

- What is SQL?
- What does SELECT do?
- What does INSERT do?
- What does UPDATE do?
- What does DELETE do?
- What does CREATE DATABASE do?
- Why does WordPress need SQL?
