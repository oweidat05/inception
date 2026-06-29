# Chapter 19 — MariaDB Container

## Introduction

In the previous chapter, we learned what MariaDB is.

Now we will study how **our MariaDB container** is built and configured.

When Docker starts the MariaDB container, it does much more than simply start a database server.

It must:

- Read Docker Secrets
- Start MariaDB
- Create the database
- Create a new user
- Grant permissions
- Stop MariaDB
- Start MariaDB again as the main container process

Everything is done automatically.

---

# Container Startup

When Docker starts the container, this happens.

```
Container Starts

↓

CMD ["/setup.sh"]

↓

Read Docker Secrets

↓

Start Temporary MariaDB

↓

Create Database

↓

Create User

↓

Grant Permissions

↓

Stop Temporary MariaDB

↓

Start MariaDB (PID 1)
```

---

# Read Docker Secrets

Your script starts with:

```bash
DB_ROOT_PASSWORD=$(cat /run/secrets/db_root_password)

DB_PASSWORD=$(cat /run/secrets/db_password)
```

Exactly like WordPress.

Docker mounted these files inside:

```
/run/secrets/
```

The script reads them and stores the values inside shell variables.

---

# Why Read Secrets First?

Because every SQL command needs a password.

Without the root password,

the script cannot connect to MariaDB.

---

# Start MariaDB

Your script starts MariaDB.

Example.

```bash
mysqld_safe &
```

or

```bash
mariadbd &
```

(depending on your implementation)

Notice the:

```
&
```

This means:

Run MariaDB in the background.

Why?

Because the script still has work to do.

If MariaDB stayed in the foreground,

the script would stop here forever.

---

# Wait Until MariaDB Is Ready

MariaDB needs a few seconds to start.

If we immediately run SQL commands,

the server may not be ready.

So the script usually waits.

Example.

```bash
until mariadb -u root -p... ; do
    sleep 1
done
```

The script keeps checking until MariaDB accepts connections.

---

# Create Database

Once MariaDB is running,

the script executes:

```sql
CREATE DATABASE wordpress;
```

Now MariaDB creates a new database called:

```
wordpress
```

This is where WordPress stores all of its tables.

---

# Create User

Next,

the script creates a new database user.

Example.

```sql
CREATE USER 'mowaidat'@'%'
IDENTIFIED BY 'password';
```

Notice something.

This user is **not** Linux.

It is a MariaDB user.

MariaDB has its own users.

Completely separate from Linux users.

---

# What Does '%' Mean?

```
'mowaidat'@'%'
```

The `%` means:

```
Any host.
```

In Docker,

WordPress connects from another container.

So MariaDB must allow connections from outside its own container.

---

# Grant Permissions

Now the user exists.

But...

The user has no permissions.

Imagine creating a new Linux user.

That user cannot automatically read every file.

The same idea applies here.

So the script runs:

```sql
GRANT ALL PRIVILEGES
ON wordpress.*
TO 'mowaidat'@'%';
```

Let's explain it.

---

# GRANT

```
GRANT
```

Give permissions.

---

# ALL PRIVILEGES

Give every permission.

Examples:

- SELECT
- INSERT
- UPDATE
- DELETE
- CREATE
- DROP

---

# wordpress.*

```
wordpress
```

means the database.

```
*
```

means:

Every table inside that database.

Examples.

```
wp_users

wp_posts

wp_comments

wp_options
```

---

# TO

Who receives the permissions?

```
mowaidat
```

---

# FLUSH PRIVILEGES

Finally,

the script executes:

```sql
FLUSH PRIVILEGES;
```

MariaDB reloads the permission tables.

Now the new user can connect immediately.

---

# Shutdown MariaDB

After configuration,

the temporary MariaDB server is stopped.

Why?

Because it was started only to execute the setup commands.

Now Docker is ready to start the real MariaDB server.

---

# Start MariaDB Again

The final line is usually something like:

```bash
exec mariadbd
```

Notice the word:

```
exec
```

Exactly like PHP-FPM.

The shell disappears.

MariaDB becomes:

```
PID 1
```

Docker now monitors MariaDB directly.

---

# Why Not Configure MariaDB Inside The Dockerfile?

Because Docker images should be reusable.

Imagine building one image.

Today:

```
Database = wordpress
```

Tomorrow:

```
Database = school
```

If everything were hardcoded inside the image,

you would need to rebuild it.

Instead,

the configuration happens when the container starts.

This makes the image reusable.

---

# Your Complete Startup

```
Docker

↓

Start Container

↓

setup.sh

↓

Read Secrets

↓

Start Temporary MariaDB

↓

Create Database

↓

Create User

↓

Grant Permissions

↓

Flush Privileges

↓

Stop Temporary Server

↓

exec mariadbd

↓

MariaDB (PID 1)

↓

Waiting For WordPress
```

---

# Key Points

Your setup script:

- reads Docker Secrets
- starts MariaDB temporarily
- creates the WordPress database
- creates a MariaDB user
- grants permissions
- starts MariaDB as PID 1 using `exec`

---

## Chapter Summary

After reading this chapter, you should be able to answer:

- Why do we start MariaDB twice?
- Why is the first MariaDB process temporary?
- What does `CREATE DATABASE` do?
- What does `CREATE USER` do?
- What does `GRANT ALL PRIVILEGES` do?
- What does `%` mean?
- Why do we use `FLUSH PRIVILEGES`?
- Why do we use `exec mariadbd`?
