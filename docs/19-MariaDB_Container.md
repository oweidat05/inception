# Chapter 19 — MariaDB Container

## Introduction

In the previous chapter, we learned what MariaDB is and why WordPress needs it.

Now we will learn how our MariaDB container is built and started.

This chapter explains every line of:

- Dockerfile
- setup.sh

By the end of this chapter, you will understand everything that happens from the moment Docker builds the image until MariaDB is ready to accept WordPress connections.

---

# Container Startup Flow

```
Docker Build

↓

Dockerfile

↓

Docker Image

↓

Container Starts

↓

CMD ["/setup.sh"]

↓

Read Docker Secrets

↓

Prepare MariaDB

↓

Initialize Database

↓

Execute SQL

↓

Start MariaDB

↓

WordPress Connects
```

---

# MariaDB Dockerfile

## FROM

```dockerfile
FROM debian:bookworm
```

Start from a clean Debian Bookworm image.

At this point,

MariaDB is **not installed**.

The image only contains a minimal Linux system.

---

## Install MariaDB

```dockerfile
RUN apt-get update && apt-get install -y \
    mariadb-server \
    && rm -rf /var/lib/apt/lists/*
```

Let's split this command.

### apt-get update

Downloads the latest package list.

Think of it like refreshing an application store.

Docker now knows which software versions are available.

---

### apt-get install

```dockerfile
mariadb-server
```

Installs the MariaDB database server.

This package includes:

- mariadbd
- SQL engine
- Client utilities
- Configuration files

After this step,

the image contains MariaDB,

but the database has **not** been created yet.

---

### rm -rf /var/lib/apt/lists/*

Delete downloaded package lists.

Why?

They are no longer needed.

Deleting them makes the Docker image smaller.

Smaller images:

- download faster
- build faster
- use less storage

---

## Copy setup.sh

```dockerfile
COPY tools/setup.sh /setup.sh
```

Copy the startup script into the image.

Notice.

Docker only copies the file.

It does **not** execute it.

---

## Make Script Executable

```dockerfile
RUN chmod +x /setup.sh
```

Linux only executes files that have execute permission.

Without this command,

Docker would fail when trying to start the container.

---

## Expose Port

```dockerfile
EXPOSE 3306
```

MariaDB listens on port:

```
3306
```

This line is documentation for the image.

It does **not** publish the port.

Docker Compose decides whether the port is accessible.

---

## CMD

```dockerfile
CMD ["/setup.sh"]
```

This is the most important line.

When Docker starts the container,

it executes:

```
/setup.sh
```

Everything else happens inside this script.

---

# setup.sh

When the container starts,

Docker runs:

```bash
/setup.sh
```

Now let's follow the script line by line.

---

## Shebang

```bash
#!/bin/bash
```

Tell Linux to execute this file using Bash.

---

## Stop On Error

```bash
set -e
```

If any command fails,

stop the script immediately.

Without this,

the script might continue after an error,

leaving MariaDB partially configured.

---

## Read Docker Secrets

```bash
DB_PASSWORD=$(cat /run/secrets/db_password)

DB_ROOT_PASSWORD=$(cat /run/secrets/db_root_password)
```

Docker mounted these secret files.

The script reads them and stores their contents in Bash variables.

Now the script knows:

- WordPress database password
- MariaDB root password

---

## Create Runtime Directory

```bash
mkdir -p /run/mysqld
```

MariaDB stores temporary runtime files here.

Examples:

- PID file
- Socket file

The directory must exist before MariaDB starts.

---

## Change Ownership

```bash
chown -R mysql:mysql /run/mysqld

chown -R mysql:mysql /var/lib/mysql
```

MariaDB runs as the Linux user:

```
mysql
```

Not:

```
root
```

The database process must have permission to read and write these directories.

---

## Configure MariaDB

```bash
sed -i \
-e 's/^bind-address.*/bind-address = 0.0.0.0/' \
-e '/bind-address/a skip-name-resolve' \
/etc/mysql/mariadb.conf.d/50-server.cnf
```

This command modifies MariaDB's configuration.

---

### bind-address

Default:

```
127.0.0.1
```

Meaning:

Only this computer can connect.

Problem:

WordPress runs inside another Docker container.

It cannot connect.

The script changes it to:

```
0.0.0.0
```

Meaning:

Accept connections from every network interface.

Now WordPress can connect through the Docker bridge network.

---

### skip-name-resolve

MariaDB normally tries to resolve hostnames into IP addresses.

Docker already provides DNS.

Skipping hostname resolution makes authentication slightly faster.

---

# Has MariaDB Already Been Initialized?

```bash
if [ ! -d "/var/lib/mysql/mysql" ]; then
```

This is a very important check.

Question:

Does the internal MariaDB system database already exist?

If:

```
/var/lib/mysql/mysql
```

exists,

MariaDB has already been initialized.

If not,

this is the first startup.

---

# Initialize Database

```bash
mariadb-install-db \
--user=mysql \
--datadir=/var/lib/mysql
```

This does **not** create the WordPress database.

Instead,

it creates MariaDB's own system databases.

Examples:

```
mysql

performance_schema

sys
```

Think of it as installing the database engine itself.

---

# Create SQL File

```bash
cat <<EOF >/tmp/init.sql
...
EOF
```

This creates a temporary SQL script.

Instead of typing SQL manually,

the script writes every SQL command into:

```
/tmp/init.sql
```

---

# ALTER USER

```sql
ALTER USER 'root'@'localhost'
IDENTIFIED BY 'password';
```

Changes the root password.

Before:

```
root

↓

No password
```

After:

```
root

↓

Protected by password
```

---

# CREATE DATABASE

```sql
CREATE DATABASE IF NOT EXISTS wordpress;
```

Creates the WordPress database.

Before:

```
mysql

performance_schema

sys
```

After:

```
mysql

performance_schema

sys

wordpress
```

---

# CREATE USER

```sql
CREATE USER 'mowaidat'@'%'
IDENTIFIED BY 'password';
```

Creates the WordPress database user.

The symbol:

```
%
```

means:

This user may connect from any host.

That includes the WordPress container.

---

# GRANT ALL PRIVILEGES

```sql
GRANT ALL PRIVILEGES
ON wordpress.*
TO 'mowaidat'@'%';
```

Give the WordPress user permission to:

- SELECT
- INSERT
- UPDATE
- DELETE
- CREATE
- DROP
- ALTER

Without these permissions,

WordPress cannot work.

---

# FLUSH PRIVILEGES

```sql
FLUSH PRIVILEGES;
```

Reload MariaDB's permission tables immediately.

Without this,

some permission changes might not be applied until later.

---

# Start MariaDB

```bash
exec mariadbd \
--user=mysql \
--init-file=/tmp/init.sql
```

MariaDB starts.

Before accepting connections,

it executes every SQL command inside:

```
/tmp/init.sql
```

After that,

the database is fully configured.

---

# Why exec?

Without:

```bash
exec
```

Process tree:

```
PID 1

↓

bash

↓

mariadbd
```

Docker watches Bash.

With:

```bash
exec
```

Process tree:

```
PID 1

↓

mariadbd
```

Docker now watches the real database process.

This is exactly what we want.

---

# Second Startup

If MariaDB was already initialized,

this block runs.

```bash
exec mariadbd --user=mysql
```

Notice.

No SQL is executed.

No database is created.

No users are recreated.

MariaDB simply starts normally.

This prevents data loss.

---

# Final Startup Flow

```
Docker Build

↓

Dockerfile

↓

Docker Image

↓

Container Starts

↓

setup.sh

↓

Read Docker Secrets

↓

Prepare Directories

↓

Modify Configuration

↓

Initialize MariaDB (First Time Only)

↓

Create SQL Script

↓

Execute SQL

↓

Start MariaDB

↓

Wait For WordPress
```

---

# Key Points

The Dockerfile:

- installs MariaDB
- copies setup.sh
- starts setup.sh

setup.sh:

- prepares MariaDB
- creates the system database
- creates the WordPress database
- creates the WordPress user
- grants permissions
- starts MariaDB as PID 1
