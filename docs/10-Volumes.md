# Chapter 10 — Docker Volumes

## Introduction

Imagine you have a MariaDB container.

Inside the database,

you create 100 users.

Then you stop the container.

Later, you accidentally remove it.

What happens to your database?

If Docker stored the database inside the container,

everything would be lost.

Docker Volumes were created to solve this problem.

---

# The Problem

Containers are temporary.

You can create them.

Stop them.

Remove them.

Create them again.

This is one of Docker's biggest advantages.

But there is a problem.

Applications like:

- MariaDB
- WordPress
- PostgreSQL
- Redis

store important data.

If the container disappears,

we do **not** want the data to disappear.

---

# What Is a Docker Volume?

A Docker Volume is a place where Docker stores data **outside the container**.

Think of it like an external hard drive.

```
Container

↓

Reads Data

↓

Docker Volume

↓

Host Computer
```

Even if the container is deleted,

the volume still exists.

---

# Container Without Volume

Imagine this container.

```
MariaDB Container

┌──────────────────────┐

Users

Tables

Database Files

└──────────────────────┘
```

Now remove the container.

```
docker rm mariadb
```

Everything disappears.

The database is gone.

---

# Container With Volume

Now use a volume.

```
MariaDB Container

        │

        ▼

Docker Volume

        │

        ▼

Host Computer
```

Now remove the container.

```
docker rm mariadb
```

The container disappears.

The volume stays.

Create a new container.

The new container reads the old database.

Nothing is lost.

---

# Your Inception Project

Look at your docker-compose.yml.

```yaml
volumes:

  mariadb_data:

  wordpress_data:
```

Docker creates two volumes.

One for MariaDB.

One for WordPress.

---

# Why Two Volumes?

MariaDB stores:

- Databases
- Tables
- Users

WordPress stores:

- Themes
- Plugins
- Uploads

Both applications need persistent storage.

---

# Mounting a Volume

Docker connects the container to the volume.

Example.

```
Host Computer

/home/mowaidat/data/mariadb

        │

        ▼

Docker Volume

        │

        ▼

/var/lib/mysql

Inside Container
```

MariaDB thinks it is writing to:

```
/var/lib/mysql
```

But Docker actually stores the files on your computer.

---

# Your docker-compose.yml

Your project contains something like this.

```yaml
volumes:

  mariadb_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /home/mowaidat/data/mariadb
```

Let's explain every line.

---

# driver

```yaml
driver: local
```

A driver tells Docker **how** to manage the volume.

The most common driver is:

```
local
```

It means:

Store the data on this computer.

No network.

No cloud.

No remote server.

Just the local disk.

---

# driver_opts

```yaml
driver_opts:
```

These are extra settings for the driver.

Think of them as configuration options.

Docker asks:

> "How should I create this volume?"

The answer is inside `driver_opts`.

---

# type

```yaml
type: none
```

This tells Linux:

Do not mount another filesystem.

We simply want to bind an existing folder.

---

# o

```yaml
o: bind
```

This is short for:

```
option
```

The value:

```
bind
```

means:

Connect this Docker volume directly to an existing folder.

Instead of creating a hidden Docker folder,

Docker uses the folder you choose.

---

# device

```yaml
device:
/home/mowaidat/data/mariadb
```

This is the folder on your computer.

Docker stores all MariaDB files here.

Picture.

```
Host Computer

/home/mowaidat/data/mariadb

        │

        ▼

Docker Volume

        │

        ▼

MariaDB Container

/var/lib/mysql
```

Now you know why this folder exists.

---

# Wait...

Do you remember asking me:

> "Does Docker really store the data in this path?"

The answer is:

**Yes.**

Every database file,

every table,

every user,

is stored inside this directory.

MariaDB simply reads and writes files there.

---

# Why Doesn't MariaDB Know This?

Inside the container,

MariaDB only sees:

```
/var/lib/mysql
```

It has no idea where the data is actually stored.

Docker performs the mapping automatically.

---

# Bind Mount vs Docker Volume

There are two common ways to store data.

### Bind Mount

```
Host Folder

↓

Container
```

Example:

```
/home/mowaidat/data

↓

/var/lib/mysql
```

You choose the folder.

---

### Docker Managed Volume

```
Docker

↓

Creates Folder

↓

Stores Data
```

Docker chooses the folder automatically.

Usually somewhere like:

```
/var/lib/docker/volumes/
```

---

# Why Does Inception Use Bind Mounts?

Because the subject says where the data should be stored.

Example.

```
/home/login/data
```

This makes it easy to find,

backup,

and inspect the files.

---

# How Can I See My Volumes?

List all volumes.

```bash
docker volume ls
```

Example.

```text
DRIVER    VOLUME NAME

local     mariadb_data

local     wordpress_data
```

---

# Inspect a Volume

```bash
docker volume inspect mariadb_data
```

Docker shows:

- Name
- Driver
- Mountpoint

Very useful when debugging.

---

# What Happens If I Remove The Container?

```
Container

❌ Removed
```

Volume

```
✅ Still Exists
```

Data is safe.

---

# What Happens If I Run

```bash
docker compose down
```

Containers disappear.

Volumes remain.

---

# What Happens If I Run

```bash
docker compose down -v
```

Containers disappear.

Volumes disappear.

Database disappears.

Use this command carefully.

---

# Key Points

Docker Volumes:

- store persistent data
- exist outside the container
- survive container removal
- can be shared with new containers
- protect your database and website files

---

## Chapter Summary

After reading this chapter, you should be able to answer:

- What is a Docker Volume?
- Why do we need volumes?
- What is `driver`?
- What is `driver_opts`?
- What does `device` mean?
- Why doesn't MariaDB lose its data?
- What is the difference between a bind mount and a Docker-managed volume?

If you cannot answer these questions, read this chapter again before moving to the next chapter.

---

➡️ **Next:** Chapter 11 — Docker Secrets
