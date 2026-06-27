# Chapter 09 — Docker Networks

## Introduction

Until now, we have built three independent containers.

* NGINX
* WordPress
* MariaDB

But there is one important question.

> **How do these containers communicate with each other?**

The answer is:

**Docker Networks.**

Without a network, containers cannot communicate.

---

# What Is a Docker Network?

A Docker Network is a virtual network created by Docker.

It allows containers to communicate with each other safely.

Think about computers in a school.

If the computers are connected to the same network,

they can exchange data.

If they are not connected,

they cannot communicate.

Docker containers work in the same way.

---

# Your Inception Project

Your project contains three containers.

```text
NGINX

↓

WordPress

↓

MariaDB
```

For this to work,

all three containers must belong to the same Docker network.

---

# Without a Network

Imagine three containers.

```
NGINX

WordPress

MariaDB
```

Each one is isolated.

NGINX cannot find WordPress.

WordPress cannot find MariaDB.

Nothing works.

---

# With a Docker Network

Now connect them.

```
             Docker Network
──────────────────────────────────

NGINX

↓

WordPress

↓

MariaDB
```

Now they can communicate.

---

# Creating a Network

Docker can create a network.

Example:

```bash
docker network create inception
```

This creates a new Docker network called:

```
inception
```

Containers connected to this network can communicate.

---

# Docker Compose

You usually do not create the network yourself.

Instead,

Docker Compose does it automatically.

Example:

```yaml
networks:

  inception:
```

When you run:

```bash
docker compose up
```

Docker Compose creates the network before starting the containers.

---

# Why Can NGINX Reach WordPress?

Look at your NGINX configuration.

```nginx
fastcgi_pass wordpress:9000;
```

Notice this:

```
wordpress
```

This is **not** an IP address.

Docker has its own DNS server.

Inside the network,

Docker automatically translates:

```
wordpress
```

↓

into

```
The IP address of the WordPress container.
```

You never need to remember IP addresses.

Docker does it for you.

---

# Another Example

Inside WordPress,

you wrote:

```bash
--dbhost=mariadb:3306
```

Again,

```
mariadb
```

is not an IP address.

Docker finds the MariaDB container automatically.

WordPress simply says:

> Connect to the container named **mariadb**.

Docker handles the rest.

---

# What Is Docker DNS?

Every container connected to the same network can find other containers by name.

Example.

```
NGINX

↓

wordpress

↓

192.168.x.x
```

Docker performs this translation automatically.

This is called **Docker DNS**.

Because of Docker DNS,

we write:

```nginx
fastcgi_pass wordpress:9000;
```

instead of

```nginx
fastcgi_pass 172.18.0.5:9000;
```

Container IP addresses may change.

Container names do not.

---

# Bridge Network

The default Docker network is called a **Bridge Network**.

Think of it as a virtual switch.

```
          Bridge Network

     ┌───────────────────────┐

     │                       │

 NGINX      WordPress     MariaDB

     │                       │

     └───────────────────────┘
```

Every container connected to the bridge can communicate.

Containers outside the bridge cannot.

---

# Your docker-compose.yml

Your project contains something similar to this.

```yaml
networks:

  inception:
    driver: bridge
```

This tells Docker:

Create a bridge network named **inception**.

Then connect all services to it.

---

# Why Not Use host?

The Inception subject forbids:

```
network_mode: host
```

Why?

Because containers would no longer be isolated.

They would use the host's network directly.

Docker networking would disappear.

This defeats one of Docker's main purposes.

---

# How Can I See My Networks?

List all networks.

```bash
docker network ls
```

Example:

```text
NETWORK ID     NAME
xxxxx          bridge
xxxxx          host
xxxxx          none
xxxxx          inception
```

---

# Inspect a Network

Example:

```bash
docker network inspect inception
```

Docker shows:

* Network name
* Driver
* Subnet
* Gateway
* Connected containers
* Container IP addresses

This is one of the most useful debugging commands.

---

# Key Points

Docker Networks:

* connect containers together
* isolate containers from other networks
* include an internal DNS service
* allow containers to communicate by name
* are automatically created by Docker Compose

---

## Chapter Summary

After reading this chapter, you should be able to answer:

* What is a Docker Network?
* Why do containers need a network?
* What is a Bridge Network?
* Why can NGINX connect to `wordpress`?
* What is Docker DNS?
* Why is `network_mode: host` forbidden in Inception?

If you cannot answer these questions, read this chapter again before moving to the next chapter.

---

➡️ **Next:** Chapter 10 — Docker Volumes
