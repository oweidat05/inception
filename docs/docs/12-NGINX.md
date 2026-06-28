# Chapter 12 — NGINX

## Introduction

So far, we have built the Docker infrastructure.

Now we will learn what happens when someone opens our website.

Imagine a user opens a browser and types:

```
https://mowaidat.42.fr
```

What happens first?

The answer is:

**NGINX.**

NGINX is the first application that receives every request.

Before WordPress executes any PHP code,

before MariaDB executes any SQL query,

the request always arrives at NGINX.

---

# What Is NGINX?

NGINX is a web server.

A web server is a program that listens for requests from clients and sends responses back.

Example.

```
Browser

↓

Request

↓

NGINX

↓

Response

↓

Browser
```

Without a web server,

a browser cannot access your website.

---

# What Is A Client?

A client is a program that requests information.

Examples:

- Google Chrome
- Firefox
- Microsoft Edge
- Safari
- curl

All of these are clients.

---

# What Is A Server?

A server is a program that provides information.

Examples:

- NGINX
- Apache
- Caddy

The server waits for requests.

When a request arrives,

it sends a response.

---

# Client And Server

Imagine ordering food.

```
Customer

↓

Waiter

↓

Kitchen
```

The customer never enters the kitchen.

The waiter receives the order,

takes it to the kitchen,

then brings the food back.

NGINX works in the same way.

```
Browser

↓

NGINX

↓

WordPress

↓

NGINX

↓

Browser
```

NGINX is the waiter.

---

# Why Do We Need NGINX?

Imagine the browser tries to communicate directly with MariaDB.

```
Browser

↓

MariaDB
```

This makes no sense.

MariaDB understands SQL,

not HTTP.

Now imagine the browser connects directly to PHP-FPM.

```
Browser

↓

PHP-FPM
```

PHP-FPM cannot serve a website.

It only executes PHP code.

Someone must receive the HTTP request first.

That is the job of NGINX.

---

# What Does NGINX Actually Do?

When a request arrives,

NGINX decides what to do.

Example.

```
Request

↓

Image?

↓

Send Image

```

Or.

```
Request

↓

PHP File?

↓

Send To PHP-FPM
```

Or.

```
Request

↓

CSS?

↓

Send CSS File
```

Everything starts with NGINX making a decision.

---

# Your Inception Project

Your browser sends:

```
https://mowaidat.42.fr
```

↓

Docker forwards the request to port 443.

↓

NGINX receives the request.

↓

NGINX checks the requested file.

↓

If it is PHP,

NGINX sends it to WordPress (PHP-FPM).

↓

WordPress talks to MariaDB.

↓

MariaDB returns data.

↓

WordPress creates HTML.

↓

NGINX sends the HTML back to the browser.

---

# The Complete Request Flow

```
Browser

↓

NGINX

↓

PHP-FPM

↓

MariaDB

↓

PHP-FPM

↓

NGINX

↓

Browser
```

This is exactly what happens every time someone opens your website.

---

# Why Is NGINX So Popular?

NGINX is:

- Fast
- Lightweight
- Stable
- Easy to configure
- Excellent for handling many connections

Many large websites use NGINX because it can serve thousands of users at the same time.

---

# Your NGINX Container

In your project,

NGINX has one job.

Receive HTTPS requests.

Forward PHP requests to WordPress.

Return the final page to the browser.

It never executes PHP.

It never stores database data.

It only receives requests and sends responses.

---

# Key Points

NGINX:

- is a web server
- receives every browser request
- serves static files
- forwards PHP requests to PHP-FPM
- returns the final response to the browser

---

## Chapter Summary

After reading this chapter, you should be able to answer:

- What is NGINX?
- What is a web server?
- What is the difference between a client and a server?
- Why can't the browser talk directly to MariaDB?
- Why can't PHP-FPM replace NGINX?
- What is NGINX's role in the Inception project?

If you cannot answer these questions, read this chapter again before moving to the next chapter.

---

➡️ **Next:** Chapter 13 — TLS (HTTPS)
