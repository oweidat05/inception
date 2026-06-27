# Chapter 03 — Docker Architecture

## Introduction

In the previous chapter, we learned why Docker was created.

Now we will answer a new question:

> **How does Docker actually work?**

When you type a Docker command, many things happen behind the scenes.

Let's discover them one by one.

---

# The Main Components

Docker is made of several parts.

The most important ones are:

- Docker CLI
- Docker Engine (Daemon)
- Docker Images
- Docker Containers
- Docker Registry

Each part has a different job.

---

# Docker CLI

CLI means:

**Command Line Interface**

This is the program you use every day.

For example:

```bash
docker ps
docker run nginx
docker images
docker logs nginx
```

The Docker CLI does **not** create containers.

It only sends your command to Docker Engine.

Think of it like a remote control.

The remote control sends a command.

The television does the work.

The Docker CLI sends the command.

Docker Engine does the work.

---

# Docker Engine

Docker Engine is also called:

**Docker Daemon**

It is a background service that is always running.

Its job is to manage everything inside Docker.

For example, it can:

- Build images
- Create containers
- Start containers
- Stop containers
- Remove containers
- Create networks
- Create volumes

The Docker Engine is the brain of Docker.

Without it, Docker cannot work.

---

# Docker Image

A Docker Image is a blueprint.

Think about building a house.

Before building the house, you need a blueprint.

The blueprint tells the workers how to build everything.

A Docker Image works the same way.

It contains everything needed to create a container.

For example:

- Linux libraries
- Application files
- Configuration files
- Installed software

An image never runs.

It is only a template.

---

# Docker Container

A container is a running image.

Example:

```text
Docker Image
        │
        ▼
docker run
        │
        ▼
Running Container
```

You can create many containers from the same image.

For example:

```text
NGINX Image

│

├── Container 1

├── Container 2

└── Container 3
```

Each container is independent.

---

# Docker Registry

A Docker Registry stores images.

The most popular registry is:

Docker Hub.

Example:

```bash
docker pull nginx
```

Docker downloads the NGINX image from Docker Hub.

If the image already exists on your computer, Docker uses the local copy.

---

# What Happens When You Run docker run nginx?

This is one of the most important things to understand.

When you type:

```bash
docker run nginx
```

Docker performs these steps.

```text
You
 │
 ▼
docker run nginx
 │
 ▼
Docker CLI
 │
 ▼
Docker Engine
 │
 ▼
Does the nginx image exist?
 │
 ├── Yes → Use it
 │
 └── No
      │
      ▼
 Download image from Docker Hub
      │
      ▼
Create Container
      │
      ▼
Start PID 1
      │
      ▼
Container is running
```

Everything happens automatically.

---

# Summary

Docker is made of several components that work together.

- Docker CLI receives your command.
- Docker Engine does the work.
- Docker Images are templates.
- Docker Containers are running applications.
- Docker Registry stores images.

Together, these components make Docker simple and powerful.

---

## Chapter Summary

After reading this chapter, you should be able to answer:

- What is Docker CLI?
- What is Docker Engine?
- What is a Docker Image?
- What is a Docker Container?
- What is Docker Hub?
- What happens after running `docker run nginx`?

If you cannot answer these questions, read this chapter again before moving to the next one.

---

➡️ **Next:** Chapter 04 — Docker Images
