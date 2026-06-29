# Chapter 17 — setup.sh

## Introduction

The WordPress Docker image contains everything needed to run WordPress.

However,

installing packages is not enough.

Every time the container starts,

it must:

- Read passwords
- Check whether WordPress is already configured
- Install WordPress if needed
- Create users
- Start PHP-FPM

All of this is done by one script.

```
setup.sh
```

Think of it as the startup manager of the WordPress container.

---

# The Complete Flow

When Docker starts the container, the following happens.

```
Container Starts

↓

CMD ["/setup.sh"]

↓

Read Docker Secrets

↓

Create PHP Runtime Directory

↓

Check wp-config.php

↓

Create wp-config.php (If Needed)

↓

Check WordPress Installation

↓

Install WordPress (If Needed)

↓

Create Author User

↓

Start PHP-FPM
```

---

# Line 1

```bash
#!/bin/bash
```

This line is called the **Shebang**.

It tells Linux:

> Execute this file using Bash.

Without it,

Linux would not know which interpreter should run the script.

---

# Line 2

```bash
set -e
```

This is one of the most important Bash options.

It means:

```
If any command fails,

stop the script immediately.
```

Imagine this.

```
Read Secret

↓

Connect Database

❌ Failed

↓

Continue Installation
```

That would be a disaster.

Instead,

the script stops immediately.

This prevents a partially configured WordPress installation.

---

# Reading Docker Secrets

```bash
DB_PASSWORD=$(cat /run/secrets/db_password)
```

Let's explain every part.

---

## cat

```bash
cat
```

Reads the contents of a file.

Example.

```
db_password

↓

SuperSecret123
```

---

## /run/secrets/db_password

Docker mounted this file when the container started.

It comes from:

```
secrets/

db_password.txt
```

on the host machine.

---

## $( )

```bash
$(...)
```

Execute the command inside the parentheses.

Take its output.

Return it.

Example.

```bash
$(date)
```

returns

```
Mon Jun 29
```

---

## Variable Assignment

Finally,

```bash
DB_PASSWORD=
```

stores the result.

After this line,

```
DB_PASSWORD
```

contains your database password.

Exactly the same thing happens here.

```bash
WP_ADMIN_PASSWORD=$(cat /run/secrets/wp_admin_password)

WP_USER_PASSWORD=$(cat /run/secrets/wp_user_password)
```

Now the script knows every password.

---

# Create PHP Runtime Directory

```bash
mkdir -p /run/php
```

Why?

PHP-FPM creates runtime files.

For example:

- PID files
- Socket files (if sockets are used)

The directory must exist before PHP-FPM starts.

---

## mkdir

```
Make Directory
```

---

## -p

```
Create the directory only if it does not already exist.
```

Without `-p`,

running the script twice would produce an error.

---

# Check wp-config.php

```bash
if [ ! -f /var/www/html/wp-config.php ]; then
```

This line asks one question.

```
Does wp-config.php exist?
```

---

## -f

Means:

```
Is this a regular file?
```

---

## !

Means:

```
NOT
```

So:

```bash
! -f
```

means:

```
The file does NOT exist.
```

---

# Why Do We Check?

Imagine restarting the container.

The file already exists.

Do we want to recreate it?

No.

Otherwise,

we would overwrite the existing configuration.

This makes the installation **idempotent**.

Running the script multiple times produces the same result.

---

# Create wp-config.php

```bash
wp config create
```

This command creates:

```
wp-config.php
```

Inside it,

WordPress stores:

- Database name
- Database user
- Database password
- Database host

No database tables are created yet.

Only the configuration file.

---

# Check WordPress Installation

```bash
if ! wp core is-installed ...
```

Another question.

```
Has WordPress already been installed?
```

If yes,

skip the installation.

If no,

install it.

---

# Why Is This Important?

Imagine restarting the container.

Without this check,

the script would try to install WordPress again.

That would fail or overwrite data.

Instead,

the script simply starts PHP-FPM.

---

# Install WordPress

```bash
wp core install
```

Now WordPress connects to MariaDB.

It creates the required database tables.

Examples:

```
wp_posts

wp_users

wp_comments

wp_options
```

It also creates the administrator account.

---

# Create Author User

```bash
wp user create
```

This inserts another row into:

```
wp_users
```

The new user receives the role:

```
Author
```

Everything is stored in MariaDB.

---

# Start PHP-FPM

The last line is:

```bash
exec php-fpm8.2 -F
```

This line deserves special attention.

---

# php-fpm8.2

Start PHP-FPM version 8.2.

Nothing surprising.

---

# -F

```
Foreground
```

Normally,

PHP-FPM runs in the background.

Docker does not like this.

Docker wants one foreground process.

So we use:

```
-F
```

to keep PHP-FPM running in the foreground.

---

# Why exec?

This is one of the most important Linux concepts.

Suppose the script ends like this.

```bash
php-fpm8.2 -F
```

The process tree looks like this.

```
setup.sh

↓

php-fpm
```

Now Bash is still PID 1.

PHP-FPM becomes its child.

Docker watches PID 1.

But Bash is no longer useful.

---

Now use:

```bash
exec php-fpm8.2 -F
```

The picture changes.

```
setup.sh

↓

exec

↓

php-fpm
```

The Bash process disappears.

PHP-FPM replaces it.

PHP-FPM becomes **PID 1**.

Docker now monitors PHP-FPM directly.

This is exactly what we want.

---

# Final Flow

```
Docker

↓

Start Container

↓

Run setup.sh

↓

Read Secrets

↓

Create Runtime Directory

↓

Generate wp-config.php

↓

Install WordPress

↓

Create Users

↓

Replace Bash With PHP-FPM

↓

PHP-FPM (PID 1)

↓
17 setup.sh
Wait For NGINX Requests
```

---

# Key Points

setup.sh:

- prepares the container
- reads Docker Secrets
- creates wp-config.php
- installs WordPress only once
- creates users
- starts PHP-FPM
- uses `exec` so PHP-FPM becomes PID 1

---

## Chapter Summary

After reading this chapter, you should be able to answer:

- Why do we use `set -e`?
- Why do we read secrets?
- Why do we check for `wp-config.php`?
- Why do we use `wp core is-installed`?
- Why do we use `mkdir -p`?
- Why do we use `exec`?
- Why must PHP-FPM become PID 1?
