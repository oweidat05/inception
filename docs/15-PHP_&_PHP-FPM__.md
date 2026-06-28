# Chapter 15 — PHP & PHP-FPM

## Introduction

In the previous chapter, we learned that WordPress is written in PHP.

This raises an important question.

> **Who executes PHP code?**

The answer is:

**PHP-FPM.**

Before understanding PHP-FPM, we must first understand PHP itself.

---

# What Is PHP?

PHP is a programming language designed for web development.

Unlike HTML,

PHP is executed on the server.

Example:

```php
<?php

echo "Hello World";

?>
```

The browser never executes this code.

Instead,

the server executes it and sends the result to the browser.

---

# HTML vs PHP

HTML is already finished.

Example.

```html
<h1>Hello World</h1>
```

The browser simply displays it.

PHP is different.

Example.

```php
<?php

echo "<h1>Hello World</h1>";

?>
```

The browser cannot understand this.

Someone must execute it first.

---

# Why Can't The Browser Execute PHP?

Imagine opening:

```
index.php
```

Inside it:

```php
<?php

echo "Hello";

?>
```

The browser does not contain a PHP interpreter.

It only understands:

- HTML
- CSS
- JavaScript

So the browser sends the file to the server.

---

# Who Executes PHP?

The answer is:

```
PHP-FPM
```

PHP-FPM stands for:

```
PHP FastCGI Process Manager
```

Its only job is to execute PHP code.

---

# Think About A Restaurant

Imagine this.

```
Customer

↓

Waiter

↓

Chef

↓

Waiter

↓

Customer
```

The customer does not enter the kitchen.

The waiter takes the order.

The chef cooks.

The waiter returns the food.

Docker works the same way.

```
Browser

↓

NGINX

↓

PHP-FPM

↓

NGINX

↓

Browser
```

NGINX is the waiter.

PHP-FPM is the chef.

---

# What Does PHP-FPM Actually Do?

Suppose NGINX receives:

```
index.php
```

NGINX cannot execute PHP.

Instead,

it forwards the request.

```
Browser

↓

NGINX

↓

PHP-FPM

↓

Execute PHP

↓

Generate HTML

↓

NGINX

↓

Browser
```

Notice.

PHP-FPM does not talk directly to the browser.

NGINX does.

---

# Why Doesn't NGINX Execute PHP?

NGINX is a web server.

Its job is:

- Receive HTTP requests
- Send HTTP responses
- Serve static files

It is **not** a PHP interpreter.

Keeping these jobs separate makes the system faster and more flexible.

---

# What Does PHP-FPM Return?

PHP-FPM receives:

```php
<?php

echo "<h1>Hello</h1>";

?>
```

It executes the PHP code.

The result becomes:

```html
<h1>Hello</h1>
```

This HTML is sent back to NGINX.

NGINX then sends it to the browser.

---

# PHP-FPM Does Not Create Websites

Many people think PHP-FPM creates WordPress.

It does not.

Think of PHP-FPM as an engine.

WordPress provides the PHP code.

PHP-FPM simply executes it.

Example.

```
WordPress

↓

PHP Code

↓

PHP-FPM

↓

HTML

↓

Browser
```

---

# FastCGI

How does NGINX talk to PHP-FPM?

They use a protocol called:

```
FastCGI
```

Look at your NGINX configuration.

```nginx
location ~ \.php$ {

    fastcgi_pass wordpress:9000;

}
```

This tells NGINX:

> Send every PHP request to PHP-FPM running inside the WordPress container.

---

# Port 9000

PHP-FPM listens on:

```
9000
```

NGINX connects to:

```
wordpress:9000
```

Docker DNS automatically finds the WordPress container.

---

# Your Project

A user opens:

```
https://mowaidat.42.fr
```

The complete flow is:

```
Browser

↓

NGINX

↓

FastCGI

↓

PHP-FPM

↓

Execute WordPress PHP Code

↓

Generate HTML

↓

NGINX

↓

Browser
```

---

# Key Points

PHP:

- is a programming language
- runs on the server
- generates HTML

PHP-FPM:

- executes PHP code
- listens on port 9000
- communicates with NGINX using FastCGI
- never talks directly to the browser

---

## Chapter Summary

After reading this chapter, you should be able to answer:

- What is PHP?
- What is PHP-FPM?
- Why can't browsers execute PHP?
- Why can't NGINX execute PHP?
- What is FastCGI?
- Why does NGINX connect to `wordpress:9000`?

If you cannot answer these questions, read this chapter again before moving to the next chapter.

---

➡️ **Next:** Chapter 16 — WordPress Configuration (www.conf, WP-CLI, wp-config.php, setup.sh)
