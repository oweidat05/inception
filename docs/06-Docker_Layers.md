# Chapter 06 — Docker Layers

## Introduction

In the previous chapters, we learned:

```
Dockerfile
      │
      ▼
Docker Image
      │
      ▼
Docker Container
```

But one important question is still missing.

> **How does Docker build an image?**

The answer is:

**Layers.**

Everything Docker builds is made of layers.

If you understand layers, you understand how Docker works.

---

# What Is a Docker Layer?

A Docker Layer is one step in building an image.

Think about building a burger.

You do not create the burger all at once.

Instead, you add one ingredient at a time.

```
Bread
  │
  ▼
Cheese
  │
  ▼
Meat
  │
  ▼
Tomato
  │
  ▼
Burger
```

Each ingredient is added one by one.

Docker works the same way.

```
FROM debian
      │
      ▼
RUN apt-get update
      │
      ▼
COPY nginx.conf
      │
      ▼
CMD nginx
      │
      ▼
Docker Image
```

Each instruction creates a new layer.

---

# Example

Look at this Dockerfile.

```dockerfile
FROM debian:bookworm

RUN apt-get update

RUN apt-get install -y nginx

COPY nginx.conf /etc/nginx/

CMD ["nginx", "-g", "daemon off;"]
```

Docker builds it like this.

```
Layer 5
CMD

────────────

Layer 4
COPY nginx.conf

────────────

Layer 3
RUN apt-get install

────────────

Layer 2
RUN apt-get update

────────────

Layer 1
FROM debian
```

Together,

all these layers become one Docker Image.

---

# Why Does Docker Use Layers?

Imagine building an image takes 10 minutes.

Now you change only one file.

Without layers,

Docker would build everything again.

```
FROM
RUN
RUN
COPY
CMD
```

Every step.

Every time.

This would be very slow.

Docker solves this problem using layers.

---

# Docker Caching

Suppose you build your image today.

```
Layer 1 ✔

Layer 2 ✔

Layer 3 ✔

Layer 4 ✔
```

Tomorrow,

you build the same Dockerfile again.

Docker asks:

```
Did Layer 1 change?

No.
```

Reuse it.

```
Did Layer 2 change?

No.
```

Reuse it.

```
Did Layer 3 change?

No.
```

Reuse it.

```
Did Layer 4 change?

No.
```

Reuse it.

Instead of rebuilding,

Docker uses the old layers.

This is called:

**Cache**

---

# You Already Saw This

Remember when you built your NGINX image?

Docker printed:

```text
CACHED [2/4] RUN apt-get update ...
CACHED [3/4] RUN mkdir ...
CACHED [4/4] COPY conf/nginx.conf ...
```

This means:

Docker checked these layers.

Nothing changed.

So it reused the old layers.

It did **not** execute the commands again.

This is why the second build finished much faster.

---

# What Happens If One Layer Changes?

Imagine this Dockerfile.

```dockerfile
FROM debian:bookworm

RUN apt-get update

RUN apt-get install -y nginx

COPY nginx.conf /etc/nginx/
```

Now you edit:

```
nginx.conf
```

Docker builds again.

```
Layer 1

FROM

✔ Reused

────────────

Layer 2

RUN apt-get update

✔ Reused

────────────

Layer 3

RUN apt-get install

✔ Reused

────────────

Layer 4

COPY nginx.conf

❌ Changed

────────────

Build New Layer
```

Only Layer 4 is rebuilt.

Everything before it is reused.

---

# But What If Layer 2 Changes?

Now change this line.

```dockerfile
RUN apt-get update
```

Docker cannot reuse anything after it.

```
Layer 1

✔ Reused

────────────

Layer 2

❌ Build Again

────────────

Layer 3

❌ Build Again

────────────

Layer 4

❌ Build Again
```

Why?

Because Layer 3 depends on Layer 2.

Layer 4 depends on Layer 3.

Changing one layer changes everything above it.

---

# Why Does Instruction Order Matter?

Imagine these two Dockerfiles.

Dockerfile A

```dockerfile
COPY . .

RUN apt-get install -y nginx
```

Every time your project changes,

Docker must rebuild everything.

Very slow.

---

Dockerfile B

```dockerfile
RUN apt-get install -y nginx

COPY . .
```

Now,

Docker reuses the installation layer.

Only your project files are copied again.

Much faster.

This is why Dockerfiles are written in a specific order.

---

# Are Layers Read-Only?

Yes.

Every layer is read-only.

Docker never changes an old layer.

Instead,

it creates a new layer.

Think about stacking books.

```
Book 4

Book 3

Book 2

Book 1
```

Docker places a new book on top.

It never changes the old books.

---

# Where Are Layers Stored?

Docker stores layers on your computer.

Different images can even share the same layers.

Example:

```
Image A

FROM debian

↓

Layer 1

----------------

Image B

FROM debian

↓

Layer 1
```

Docker downloads Debian only once.

Both images use the same base layer.

This saves disk space.

---

# Relationship Between Layers and Containers

Remember:

Images are read-only.

Containers are writable.

```
Container

Writable Layer

────────────

Image Layer 4

────────────

Image Layer 3

────────────

Image Layer 2

────────────

Image Layer 1
```

When your application writes a file,

it is saved in the writable layer,

not inside the image.

---

# Key Points

Docker Layers:

- build an image one step at a time
- are read-only
- can be reused
- make builds faster
- save disk space
- are shared between images when possible

---

## Chapter Summary

After reading this chapter, you should be able to answer:

- What is a Docker Layer?
- Why does Docker use layers?
- What does **CACHED** mean?
- Why does changing one line rebuild only part of the image?
- Why should Dockerfiles be written in a specific order?
- Why are layers read-only?

If you cannot answer these questions, read this chapter again before moving to the next chapter.

---

➡️ **Next:** Chapter 07 — Dockerfile
