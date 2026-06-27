# Chapter 08 — Docker Compose

## Introduction

In the previous chapters, we learned how to build Docker images and create containers.

Imagine that your application contains only one container.

For example:

```
NGINX
```

Starting it is simple.

```bash
docker run nginx
```

Now imagine a real web application.

It needs:

* NGINX
* WordPress
* MariaDB

Should we start every container manually?

Should we remember every command?

Should we create the network ourselves?

Should we create the volumes ourselves?

This would quickly become difficult.

Docker Compose was created to solve this problem.

---

# What Is Docker Compose?

Docker Compose is a tool that manages multiple Docker containers.

Instead of typing many Docker commands,

you describe your entire infrastructure inside one file.

This file is called:

```
docker-compose.yml
```

Docker Compose reads this file and creates everything automatically.

---

# Before Docker Compose

Without Docker Compose, you would need to execute many commands.

Example:

```bash
docker network create inception

docker volume create mariadb_data

docker volume create wordpress_data

docker build -t mariadb .

docker build -t wordpress .

docker build -t nginx .

docker run ...

docker run ...

docker run ...
```

This is possible,

but it is slow,

easy to forget,

and difficult to maintain.

---

# With Docker Compose

Instead,

you write one file.

```
docker-compose.yml
```

Then run one command.

```bash
docker compose up
```

Docker Compose reads the file and performs all the work for you.

---

# What Happens Behind the Scenes?

When you type:

```bash
docker compose up --build
```

Docker Compose performs these steps.

```
Read docker-compose.yml
            │
            ▼
Build Images
            │
            ▼
Create Network
            │
            ▼
Create Volumes
            │
            ▼
Create Containers
            │
            ▼
Start Containers
```

Everything happens automatically.

---

# Your Inception Project

Your project contains three services.

```
NGINX

↓

WordPress

↓

MariaDB
```

Docker Compose reads your file and creates these three containers.

It also creates:

* Docker Network
* Docker Volumes
* Secrets
* Environment Variables

Everything from one configuration file.

---

# Why Is This Better?

Imagine you change your computer.

Without Docker Compose,

you must remember every command.

With Docker Compose,

you simply copy your project and run:

```bash
docker compose up --build
```

A few minutes later,

your entire infrastructure is running.

---

# docker-compose.yml

A Docker Compose file describes your infrastructure.

Example:

```yaml
services:

  nginx:

    build: ./requirements/nginx

  wordpress:

    build: ./requirements/wordpress

  mariadb:

    build: ./requirements/mariadb
```

Notice something.

We never call:

```bash
docker build
```

ourselves.

Docker Compose does it automatically.

---

# build

Example:

```yaml
build: ./requirements/nginx
```

This tells Docker Compose:

Go to this folder.

↓

Find the Dockerfile.

↓

Run:

```bash
docker build
```

↓

Create the image.

---

# container_name

Example:

```yaml
container_name: nginx
```

Without this line,

Docker generates a random name.

Example:

```
happy_penguin
```

With this line,

the container is always called:

```
nginx
```

---

# Why Doesn't Docker Compose Create Images Every Time?

Remember Docker Layers.

Docker checks:

```
Did anything change?
```

If nothing changed,

Docker reuses the cached layers.

The build becomes very fast.

---

# docker compose up

This command creates and starts your infrastructure.

Example:

```bash
docker compose up
```

If the containers already exist,

Docker starts them.

If they do not exist,

Docker creates them.

---

# docker compose up --build

Example:

```bash
docker compose up --build
```

This command first rebuilds the images.

Then it starts the containers.

Use this command after changing a Dockerfile.

---

# docker compose down

Example:

```bash
docker compose down
```

This command stops and removes:

* Containers
* Network

Your volumes are kept unless you add:

```bash
-v
```

---

# docker compose down -v

This removes:

* Containers
* Network
* Volumes

Be careful.

This deletes persistent data.

For example,

your MariaDB database.

---

# Key Points

Docker Compose:

* manages multiple containers
* reads `docker-compose.yml`
* builds images
* creates containers
* creates networks
* creates volumes
* starts everything with one command

---

## Chapter Summary

After reading this chapter, you should be able to answer:

* What is Docker Compose?
* Why do we use Docker Compose?
* What does `docker compose up` do?
* What does `--build` do?
* What is `docker-compose.yml`?
* Why is Docker Compose useful in the Inception project?

If you cannot answer these questions, read this chapter again before moving to the next chapter.

---

➡️ **Next:** Chapter 09 — Docker Networks
