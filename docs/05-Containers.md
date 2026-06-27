# Chapter 05 — Docker Containers

## Introduction

In the previous chapter, we learned that a Docker Image is only a template.

An image cannot run by itself.

To run an application, Docker creates a **container** from the image.

This chapter explains what a container is and how Docker creates one.

---

# What Is a Docker Container?

A Docker Container is a **running instance of a Docker Image**.

Think about a blueprint.

A blueprint is only a drawing.

Nobody can live inside a blueprint.

To create a house, workers must build it.

Docker works the same way.

```
Blueprint
      │
      ▼
House

Docker Image
      │
      ▼
Docker Container
```

An image is only a template.

A container is the running application.

---

# Creating Your First Container

Suppose you already have the NGINX image.

Check your images:

```bash
docker images
```

Example:

```text
REPOSITORY          TAG
nginx              inception
```

Now create a container.

```bash
docker run nginx:inception
```

Docker creates a new container from the image.

The image stays unchanged.

---

# One Image, Many Containers

One image can create many containers.

Example:

```
             nginx:inception
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
   Container 1  Container 2  Container 3
```

Every container has:

- Its own processes
- Its own memory
- Its own network
- Its own writable layer

Containers do not share these resources directly.

If one container crashes,

the others continue running.

---

# What Happens When You Run docker run?

When you type:

```bash
docker run nginx:inception
```

Docker performs these steps.

```
docker run nginx:inception
           │
           ▼
Find Image
           │
           ▼
Create Container
           │
           ▼
Create Writable Layer
           │
           ▼
Start PID 1
           │
           ▼
Container Running
```

Everything happens automatically.

---

# What Is the Writable Layer?

Remember that images are read-only.

Docker cannot modify an image.

Instead,

Docker creates a small writable layer above the image.

```
        Writable Layer
               ▲
               │
        Container
               │
               ▼
        Docker Image
```

When a program writes data,

the changes are stored inside the writable layer.

The original image never changes.

---

# Why Does Docker Do This?

Imagine you have one image.

```
Ubuntu Image
```

Now create two containers.

```
Ubuntu Image
      │
 ┌────┴────┐
 ▼         ▼
Container1 Container2
```

If Container 1 creates a file,

Container 2 cannot see it.

Both containers have their own writable layer.

This allows many containers to use the same image safely.

---

# Stopping a Container

A container keeps running while its main process is running.

For example,

inside your NGINX container,

PID 1 is:

```
nginx -g "daemon off;"
```

If PID 1 stops,

the container stops.

This is why we wrote:

```dockerfile
CMD ["nginx", "-g", "daemon off;"]
```

instead of running NGINX in the background.

We will study PID 1 in detail later in this guide.

---

# Container vs Image

Docker Image

- Template
- Read-only
- Cannot run
- Used to create containers

Docker Container

- Running application
- Writable
- Has its own processes
- Created from an image

---

# Key Points

A container:

- is created from an image
- runs your application
- has its own writable layer
- has its own PID 1 process
- stops when PID 1 stops

---

## Chapter Summary

After reading this chapter, you should be able to answer:

- What is a Docker Container?
- What is the difference between an image and a container?
- Why can one image create many containers?
- What is the writable layer?
- Why does the container stop when PID 1 exits?

If you cannot answer these questions, read this chapter again before moving to the next one.

---

➡️ **Next:** Chapter 06 — Docker Layers
