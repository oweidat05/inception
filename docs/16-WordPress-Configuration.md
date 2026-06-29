# Chapter 16 — WP-CLI & WordPress Configuration

## Introduction

During the WordPress container startup, your `setup.sh` script executes three important commands.

```bash
wp config create

wp core install

wp user create
```

These three commands prepare WordPress before PHP-FPM starts.

But...

What is `wp`?

Many students think it is part of MariaDB.

Others think it is a Linux command.

Neither is correct.

---

# What Is WP-CLI?

WP-CLI stands for:

```
WordPress Command Line Interface
```

It is a command-line application that allows you to control WordPress without using a browser.

Normally, you install WordPress by opening:

```
https://yourwebsite.com
```

Then you click:

- Next
- Install
- Create Admin
- Finish

WP-CLI performs these same actions automatically from the terminal.

---

# Is WP-CLI WordPress?

No.

Think of WordPress as a car.

WP-CLI is the remote control.

```
WP-CLI

↓

Controls

↓

WordPress
```

WP-CLI does not replace WordPress.

It simply tells WordPress what to do.

---

# Is WP-CLI MariaDB?

No.

WP-CLI never stores data itself.

Instead,

it asks WordPress to connect to MariaDB.

```
WP-CLI

↓

WordPress

↓

MariaDB
```

WordPress performs the database operations.

---

# Is WP-CLI PHP?

Almost.

WP-CLI is written in PHP.

When you type:

```bash
wp core install
```

Linux executes:

```
wp
```

↓

which starts

```
PHP
```

↓

which loads

```
WordPress
```

↓

which executes the requested command.

So WP-CLI is a PHP application.

```
wp

↓

PHP

↓

WordPress
```

---

# wp config create

This is the first command.

```bash
wp config create \
  --dbname=wordpress \
  --dbuser=mowaidat \
  --dbpass=password \
  --dbhost=mariadb
```

Notice something.

There is no SQL yet.

MariaDB is not modified.

This command only creates one file.

```
wp-config.php
```

---

# What Is wp-config.php?

Think of it as the settings file for WordPress.

Inside it are the database connection details.

```php
define('DB_NAME', 'wordpress');

define('DB_USER', 'mowaidat');

define('DB_PASSWORD', '********');

define('DB_HOST', 'mariadb');
```

Later,

every PHP page reads this file before connecting to MariaDB.

---

# wp core install

Now WordPress finally knows where MariaDB is.

The next command is:

```bash
wp core install
```

This command connects to MariaDB.

It checks:

```
Does the WordPress database already exist?
```

If not,

it creates the required tables.

---

# Which Tables?

Examples:

```
wp_users

wp_posts

wp_comments

wp_options

wp_terms

wp_usermeta
```

These are the tables you saw when you ran:

```sql
SHOW TABLES;
```

inside MariaDB.

---

# It Also Creates The Admin User

The command:

```bash
wp core install
```

does more than create tables.

It also inserts:

- Website title
- Administrator username
- Administrator email
- Administrator password

These values are stored inside MariaDB.

---

# wp user create

Finally,

your script runs:

```bash
wp user create
```

This command inserts another user.

Example:

```
Username

↓

author
```

Role:

```
Author
```

WP-CLI sends SQL queries through WordPress,

and MariaDB stores the new user in:

```
wp_users
```

---

# Does WP-CLI Write SQL?

No.

WP-CLI calls WordPress functions.

WordPress creates the SQL queries.

MariaDB executes them.

```
WP-CLI

↓

WordPress

↓

SQL

↓

MariaDB
```

WP-CLI never talks directly to MariaDB.

---

# Why Doesn't It Need A Browser?

Normally,

installation looks like this.

```
Browser

↓

Install Wizard

↓

WordPress

↓

MariaDB
```

WP-CLI skips the browser.

```
Terminal

↓

WP-CLI

↓

WordPress

↓

MariaDB
```

The same installation happens,

just without clicking buttons.

---

# Your setup.sh

Your script executes the commands in the correct order.

```
Create wp-config.php

↓

Install WordPress

↓

Create Author User

↓

Start PHP-FPM
```

Everything is automated.

This is why your WordPress container starts fully configured.

---

# Key Points

WP-CLI:

- is a command-line tool for WordPress
- is written in PHP
- controls WordPress
- does not connect directly to MariaDB
- creates `wp-config.php`
- installs WordPress
- creates users

---

## Chapter Summary

After reading this chapter, you should be able to answer:

- What is WP-CLI?
- Is WP-CLI part of MariaDB?
- What does `wp config create` do?
- What does `wp core install` do?
- What does `wp user create` do?
- Why doesn't WP-CLI need a browser?
