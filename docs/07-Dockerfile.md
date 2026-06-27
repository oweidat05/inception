# Chapter 07 — Dockerfile

## Introduction

In the previous chapter, we learned that Docker builds an image one layer at a time.

Now we will answer a new question.

> **How do we tell Docker what image to build?**

The answer is:

**Dockerfile.**

A Dockerfile is simply a text file that contains instructions.

Docker reads these instructions from top to bottom and builds an image step by step.

---

# What Is a Dockerfile?

A Dockerfile is a text file that tells Docker how to build an image.

Think about cooking.

Before you cook a meal, you need a recipe.

The recipe tells you:

- Which ingredients to use
- Which order to follow
- How to prepare the food

A Dockerfile works in the same way.

Instead of cooking food,

it builds a Docker image.

---

# Your First Dockerfile

Here is a simple example.

```dockerfile
FROM debian:bookworm

RUN apt-get update

RUN apt-get install -y nginx

COPY nginx.conf /etc/nginx/

CMD ["nginx", "-g", "daemon off;"]
```

Docker reads this file from top to bottom.

It never starts from the bottom.

---

# How Docker Reads a Dockerfile

Imagine Docker is reading your file.

```
Read Line 1

↓

Execute

↓

Read Line 2

↓

Execute

↓

Read Line 3

↓

Execute

↓

Image Finished
```

Every instruction changes the image.

Some instructions create filesystem layers.

Some instructions only add metadata.

---

# FROM

Every Dockerfile starts with:

```dockerfile
FROM debian:bookworm
```

This tells Docker:

> "Use the Debian Bookworm image as the starting point."

Docker now asks:

```
Do I already have debian:bookworm?
```

If the answer is:

```
Yes
```

Docker uses the local image.

If the answer is:

```
No
```

Docker downloads it from Docker Hub.

Only after the base image is ready can Docker continue building.

Without a `FROM` instruction, Docker does not know where to start.

---

# RUN

Example:

```dockerfile
RUN apt-get update
```

RUN executes a command **while the image is being built**.

This is very important.

RUN does **not** execute when the container starts.

It executes only during:

```
docker build
```

Example:

```dockerfile
RUN apt-get install -y nginx
```

Docker starts a temporary container.

Inside that temporary container it executes:

```
apt-get install -y nginx
```

When the command finishes,

Docker saves the result as a new image layer.

Then the temporary container is removed.

The installed software stays inside the image.

---

# COPY

Example:

```dockerfile
COPY nginx.conf /etc/nginx/
```

COPY copies files from your computer into the image.

Source:

```
Your Computer
```

Destination:

```
Docker Image
```

Picture:

```
Your Computer

nginx.conf

        │

        ▼

Docker Image

/etc/nginx/nginx.conf
```

COPY does **not** copy files from the Internet.

It copies files from the build context.

---

# What Is the Build Context?

Imagine this project.

```
project/

├── Dockerfile

├── nginx.conf

└── index.html
```

You build it using:

```bash
docker build .
```

The dot (`.`) means:

> Send the current folder to Docker.

Docker now receives:

```
Dockerfile

nginx.conf

index.html
```

Now COPY can access these files.

If a file is outside the build context,

Docker cannot copy it.

---

# CMD

Example:

```dockerfile
CMD ["nginx", "-g", "daemon off;"]
```

CMD tells Docker:

> "When a container starts, run this command."

Notice the difference.

RUN

↓

Executed during:

```
docker build
```

CMD

↓

Executed during:

```
docker run
```

RUN builds the image.

CMD starts the application.

---

# RUN vs CMD

Many beginners confuse these instructions.

Think about building a house.

RUN

```
Install doors

Install windows

Paint the walls
```

These jobs happen while building the house.

CMD

```
Open the front door.
```

This happens after the house is finished.

Docker works the same way.

---

# Why Does Your NGINX Dockerfile Use CMD?

Your Dockerfile contains:

```dockerfile
CMD ["nginx", "-g", "daemon off;"]
```

When Docker creates a container,

this command becomes PID 1.

```
Container Starts

↓

CMD

↓

nginx

↓

PID 1

↓

Container Running
```

If NGINX stops,

PID 1 exits.

The container also stops.

---

# Why Don't We Write This?

```dockerfile
RUN nginx
```

Because RUN executes only while building the image.

Docker would:

```
Start Temporary Container

↓

Run nginx

↓

Finish Layer

↓

Remove Temporary Container
```

The final image would not keep NGINX running.

Instead,

we use CMD so NGINX starts every time a new container starts.

---

# EXPOSE

Example:

```dockerfile
EXPOSE 443
```

EXPOSE does **not** open the port.

It simply documents that the application inside the container uses port 443.

The real port mapping happens later.

Example:

```yaml
ports:
  - "443:443"
```

inside `docker-compose.yml`.

---

# Building an Image

After writing the Dockerfile,

build the image.

```bash
docker build -t nginx:inception .
```

Docker now performs these steps.

```
Read Dockerfile

↓

FROM

↓

RUN

↓

COPY

↓

CMD

↓

EXPOSE

↓

Create Image

↓

Store Image
```

The image is now ready.

---

# Key Points

Dockerfile

- is a text file
- builds an image
- is read from top to bottom

FROM

- selects the base image

RUN

- executes commands during `docker build`

COPY

- copies files from the build context

CMD

- starts the main application when the container starts

EXPOSE

- documents the port used by the application

---

## Chapter Summary

After reading this chapter, you should be able to answer:

- What is a Dockerfile?
- Why must every Dockerfile start with `FROM`?
- What is the difference between `RUN` and `CMD`?
- What does `COPY` do?
- What is the build context?
- Does `EXPOSE` actually open a port?

If you cannot answer these questions, read this chapter again before moving to the next one.

---

➡️ **Next:** Chapter 08 — Docker Compose
