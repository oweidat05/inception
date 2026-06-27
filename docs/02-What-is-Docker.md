# Chapter 02 — What Is Docker?

## Introduction

Imagine you have spent two weeks developing an application on your computer.

Everything works perfectly.

You send the project to your friend.

A few minutes later, your friend replies:

> **"It doesn't work on my machine."**

This sentence became one of the biggest problems in software development.

The application worked perfectly on the developer's computer, but failed on another machine because the environment was different.

Docker was created to solve this problem.

---

# The Problem Before Docker

Every application depends on many components to run correctly.

For example, a web application may require:

* Linux
* PHP
* NGINX
* MariaDB
* PHP extensions
* Libraries
* Environment variables
* Configuration files

If just one component is different, the application may stop working.

For example:

Developer:

```text
Ubuntu 24.04
PHP 8.2
MariaDB 10.11
```

Friend:

```text
Ubuntu 22.04
PHP 7.4
MariaDB 10.5
```

Even though both computers are running Linux, the application may fail because the software versions are different.

This problem became known as:

> **"It works on my machine."**

---

# Traditional Solution

Before Docker, developers usually installed every dependency directly on the operating system.

Example:

```text
Operating System
│
├── PHP
├── NGINX
├── MariaDB
├── Python
├── Node.js
└── Libraries
```

This worked, but created several problems.

If one application needed PHP 7.4 while another required PHP 8.2, both applications could conflict with each other.

Updating one application could accidentally break another.

The operating system became difficult to maintain.

---

# Why Virtual Machines Were Not Enough

Virtual Machines solved part of the problem.

Each virtual machine contained:

* Its own operating system
* Its own libraries
* Its own applications

Example:

```text
Computer
│
├── VM 1
│   ├── Ubuntu
│   ├── PHP
│   └── MariaDB
│
└── VM 2
    ├── Debian
    ├── Node.js
    └── PostgreSQL
```

This isolated applications from each other.

However, virtual machines were heavy.

Every VM included an entire operating system, consuming large amounts of RAM, CPU, and disk space.

Starting a virtual machine could take minutes.

---

# Docker's Idea

Instead of creating a complete operating system for every application, Docker isolates only the application and everything it needs.

The operating system kernel is shared with the host machine.

This makes containers much smaller and much faster than virtual machines.

Instead of running:

```text
Application
↓

Virtual Machine
↓

Guest Operating System
↓

Hardware
```

Docker runs:

```text
Application
↓

Container
↓

Docker Engine
↓

Host Operating System
↓

Hardware
```

Because containers share the host kernel, they start in seconds and use far fewer system resources.

---

# What Is Docker?

Docker is a platform that allows developers to package an application together with all of its dependencies into an isolated environment called a **container**.

A container includes everything required to run the application:

* Application code
* Libraries
* Runtime
* Configuration files
* System tools

As long as Docker is installed, the container behaves the same regardless of the host operating system.

This means the developer, tester, and production server all run the exact same environment.

---

# Docker In The Inception Project

In this project we will create three independent containers.

```text
NGINX
│
▼
WordPress (PHP-FPM)
│
▼
MariaDB
```

Each service runs inside its own isolated environment.

Instead of installing NGINX, PHP, and MariaDB directly on Fedora or Debian, Docker runs each service independently.

This makes the infrastructure easier to build, maintain, and reproduce.

---

# Key Takeaways

After reading this chapter, you should understand:

* Why software environments become inconsistent.
* Why "It works on my machine" became a common problem.
* Why Virtual Machines were introduced.
* Why Docker is lighter than a Virtual Machine.
* What Docker is.
* Why Docker is used in the Inception project.

---

## Chapter Summary

Docker was not created simply to run containers.

It was created to solve the problem of inconsistent software environments.

By packaging an application together with everything it needs, Docker allows developers to build once and run the same application consistently on any machine that supports Docker.

In the next chapter, we will look inside Docker itself and learn how Docker is organized internally.

➡️ **Next:** Chapter 03 — Docker Architecture.
