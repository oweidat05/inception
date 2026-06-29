# Chapter 19 — MariaDB Dockerfile

## Introduction

The MariaDB container is responsible for one thing.

Store and manage the WordPress database.

Before MariaDB can do that,

we first need to build the Docker image.

This is done using the Dockerfile.

---

# Dockerfile

```dockerfile
FROM debian:bookworm
```

Start from the Debian Bookworm base image.

This image contains only a minimal Linux system.

There is no MariaDB installed yet.

---

```dockerfile
RUN apt-get update && apt-get install -y \
    mariadb-server \
    && rm -rf /var/lib/apt/lists/*
```

This command does three things.

---

## apt-get update

Downloads the latest package list.

Think of it like updating an app store.

Docker now knows which packages are available.

---

## apt-get install

```dockerfile
mariadb-server
```

Installs the MariaDB database server.

This package includes:

- mariadbd
- mariadb client
- SQL engine
- configuration files

After this command,

MariaDB exists inside the image.

---

## rm -rf /var/lib/apt/lists/*

Deletes downloaded package lists.

Why?

Because we no longer need them.

This reduces the image size.

A smaller image:

- downloads faster
- uses less disk space
- builds faster

---

```dockerfile
COPY tools/setup.sh /setup.sh
```

Copies your startup script into the image.

Notice something.

Docker does **not** execute it here.

It only copies the file.

---

```dockerfile
RUN chmod +x /setup.sh
```

Makes the script executable.

Without this permission,

Linux would refuse to run it.

---

```dockerfile
EXPOSE 3306
```

Documents that MariaDB listens on port:

```
3306
```

This does **not** publish the port to your computer.

It simply tells anyone reading the image:

> MariaDB uses port 3306.

Docker Compose decides whether the port is actually accessible.

---

```dockerfile
CMD ["/setup.sh"]
```

This is the most important line.

When Docker starts the container,

it runs:

```bash
/setup.sh
```

Everything that happens next is controlled by your script.

```
Container Starts

↓

setup.sh

↓

MariaDB Starts
```

---

# Summary

The Dockerfile does not create databases.

It does not create users.

It does not execute SQL.

Its job is only to prepare the image.

The real configuration happens later,

inside `setup.sh`.
