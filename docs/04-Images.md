# Chapter 04 — Docker Images

## Introduction

In the previous chapter, we learned how Docker is organized internally.

We discovered that Docker uses:

- Docker CLI
- Docker Engine
- Docker Images
- Docker Containers

In this chapter, we will focus on one question.

> **What is a Docker Image?**

---

# Before We Begin

Let's answer a simple question.

When you build your Inception project, the first line in your Dockerfile is:

```dockerfile
FROM debian:bookworm
```

Where does this Debian come from?

Does Docker create Debian?

No.

Docker downloads it from Docker Hub.

After downloading it once, Docker stores it on your computer.

Later, every new container can use the same image without downloading it again.

---

# What Is A Docker Image?

A Docker Image is a **template** used to create containers.

Think about building a house.

Before construction starts, an architect creates a blueprint.

The blueprint describes:

- Rooms
- Doors
- Windows
- Walls

But nobody can live inside a blueprint.

The blueprint only describes how to build the house.

A Docker Image works exactly the same way.

It describes:

- Which operating system to use
- Which software to install
- Which files to copy
- Which command to run when the container starts

An image is **not running**.

It is simply a recipe for creating containers.

---

# Blueprint vs House

Blueprint

↓

House

Docker Image

↓

Docker Container

Just as many houses can be built from the same blueprint,

many containers can be created from the same image.

Example:

```text
          NGINX Image
               │
     ┌─────────┼─────────┐
     ▼         ▼         ▼
 Container1 Container2 Container3
```

Every container is independent.

If one container crashes,

the others continue running.

---

# Where Are Images Stored?

Docker stores downloaded images locally on your computer.

Check them using:

```bash
docker images
```

Example:

```text
REPOSITORY      TAG
debian          bookworm
nginx           latest
wordpress       latest
```

These images are now stored on your machine.

Docker does **not** download them again unless they are removed.

---

# Where Do Images Come From?

Images are usually downloaded from Docker Hub.

For example:

```bash
docker pull nginx
```

Let's explain every word.

```text
docker
```

The Docker program.

```text
pull
```

Download an image.

```text
nginx
```

The name of the image.

So this command means:

> Download the NGINX image from Docker Hub.

---

# What Happens When You Run docker run nginx?

Imagine your computer does not have the NGINX image.

You type:

```bash
docker run nginx
```

Docker performs these steps automatically.

```text
docker run nginx
        │
        ▼
Do I already have nginx?
        │
   ┌────┴────┐
   │         │
  YES       NO
   │         │
   ▼         ▼
Use Image  Download Image
               │
               ▼
        Create Container
               │
               ▼
          Start Container
```

This is one of Docker's best features.

---

# Images Are Read-Only

This is one of the most important ideas in Docker.

A Docker Image cannot change.

Think about a PDF file.

Everyone can read it.

Nobody changes the original file while reading it.

Docker Images work the same way.

When Docker starts a container,

it never changes the original image.

Instead,

Docker creates a small writable layer on top of the image.

The container writes all new data into this writable layer.

The original image always stays unchanged.

---

# Why Is This Useful?

Imagine you have one NGINX image.

```text
NGINX Image
```

Now create three containers.

```text
NGINX Image
     │
 ┌───┼───┐
 ▼   ▼   ▼
 C1  C2  C3
```

Each container has its own writable layer.

Changing files inside Container 1 does **not** change:

- Container 2
- Container 3
- The original image

This makes Docker efficient.

---

# Key Points

A Docker Image:

- is not running
- is read-only
- is a template
- contains everything needed to create a container

A Docker Container:

- is created from an image
- is running
- has its own writable layer

When you complete building this project, you will learn how to generate images using commands

docker build -t mariadb:inception srcs/requirements/mariadb

build mean create image 
-t mean name image 
mariadb:inc.... name image 
srcs/requirements/nginx This is the build context
Docker goes into this folder and looks for: Dockerfile Automatically.



---

## Chapter Summary

After reading this chapter, you should be able to answer:

- What is a Docker Image?
- Where do images come from?
- What does `docker pull` do?
- Why is an image read-only?
- What is the difference between an image and a container?

If you cannot answer these questions, read this chapter again before moving to the next one.

---

➡️ **Next:** Chapter 05 — Docker Containers
