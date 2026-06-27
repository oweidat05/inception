# Introduction

Welcome!

This guide was created to explain the **42 Inception** project from the ground up.

The purpose of this repository is not only to provide a working solution, but also to explain every technology used throughout the project.

Instead of simply following tutorials, we will understand how every component works, why it exists, and how all services communicate together.

By the end of this guide, you will not only know how to build the project—you will understand what happens behind the scenes every time a user opens the website.

---

## What Is Inception?

Inception is a system administration project in the 42 curriculum.

The goal is to build a complete web infrastructure using Docker.

Instead of installing every service directly on the operating system, each service runs inside its own isolated container.

These containers communicate through Docker networking and together create a complete production-like environment.

---

## Technologies Used

Throughout this project we will explore:

- Docker
- Docker Compose
- Linux
- NGINX
- TLS (HTTPS)
- WordPress
- PHP-FPM
- MariaDB
- Docker Volumes
- Docker Networks
- Docker Secrets

---

## Final Infrastructure

The final architecture looks like this.

*(Architecture Diagram Here)*

---

## What You Will Learn

By completing this guide you will understand:

- Why Docker exists
- What problems containers solve
- The difference between Images and Containers
- How Docker networking works
- How Docker volumes preserve data
- How NGINX communicates with PHP-FPM
- How PHP communicates with MariaDB
- How TLS encrypts traffic
- Why PID 1 is important
- How Docker Compose manages multiple containers

---

## Before We Start

Many tutorials explain Docker by telling you what commands to type.

This guide takes a different approach.

Every chapter answers four questions:

- What is it?
- Why does it exist?
- How does it work?
- What happens if we remove it?

Understanding these questions is the difference between copying commands and becoming an engineer.

Now let's begin our journey by answering the first question:

**What is Docker?**
