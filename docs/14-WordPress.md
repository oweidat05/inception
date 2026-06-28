# Chapter 14 — WordPress

## Introduction

So far, we have learned how Docker, NGINX, and TLS work together.

Now we will answer another important question.

> **What is WordPress?**

Many beginners think WordPress is a web server.

It is not.

Some think WordPress is a database.

It is not.

Some think WordPress is a programming language.

It is not.

WordPress is a **Content Management System (CMS).**

---

# What Is a CMS?

CMS stands for:

```
Content Management System
```

A CMS is software that allows people to build and manage websites without writing all the code themselves.

Imagine you want to create a blog.

Without WordPress, you would need to write:

- HTML
- CSS
- JavaScript
- PHP
- SQL

This requires programming knowledge.

With WordPress,

you simply open a browser.

```
New Post

↓

Write Title

↓

Write Content

↓

Publish
```

WordPress creates the website for you.

---

# What Does WordPress Actually Do?

WordPress manages website content.

Examples:

- Blog posts
- Pages
- Users
- Comments
- Images
- Themes
- Plugins

WordPress stores all this information and displays it as a website.

---

# Is WordPress a Web Server?

No.

WordPress does not listen for browser requests.

NGINX does.

```
Browser

↓

NGINX

↓

WordPress
```

NGINX receives the request.

WordPress generates the page.

---

# Is WordPress a Database?

No.

WordPress stores information inside MariaDB.

```
WordPress

↓

MariaDB
```

WordPress asks the database questions like:

```
Give me the latest blog posts.

Give me the user named "admin".

Save this new comment.
```

MariaDB stores the data.

WordPress uses it.

---

# Why Does WordPress Need PHP?

WordPress is written in PHP.

That means every time someone opens the website,

PHP code must be executed.

Example.

```
index.php
```

This is not HTML.

It is PHP code.

Someone must execute it.

That "someone" is PHP-FPM.

---

# Why Can't NGINX Execute PHP?

NGINX is a web server.

It understands HTTP.

It does **not** understand PHP.

Imagine giving NGINX this file.

```php
<?php

echo "Hello";

?>
```

NGINX cannot execute it.

Instead,

NGINX sends it to PHP-FPM.

---

# What Happens When Someone Opens WordPress?

Imagine a user visits:

```
https://mowaidat.42.fr
```

The process looks like this.

```
Browser

↓

NGINX

↓

index.php

↓

PHP-FPM

↓

WordPress

↓

MariaDB

↓

WordPress

↓

HTML

↓

NGINX

↓

Browser
```

Notice something.

The browser never sees PHP code.

It only receives HTML.

---

# Where Is WordPress Stored?

Inside your WordPress container.

Example.

```
/var/www/html
```

This directory contains files such as:

```
index.php

wp-config.php

wp-admin/

wp-content/

wp-includes/
```

These files make up the WordPress application.

---

# wp-config.php

One of the most important files is:

```
wp-config.php
```

This file tells WordPress how to connect to the database.

Example.

```php
define('DB_NAME', 'wordpress');

define('DB_USER', 'mowaidat');

define('DB_PASSWORD', '********');

define('DB_HOST', 'mariadb');
```

Without this file,

WordPress does not know where its database is.

---

# What Happens During Installation?

When you run:

```bash
wp core install
```

WP-CLI does **not** install PHP.

It does **not** install MariaDB.

Instead,

it prepares the WordPress application.

It creates the required database tables.

It creates the administrator account.

It stores the website title.

It finishes the initial setup.

---

# Where Are Posts Stored?

Imagine you publish a new article.

```
My First Post
```

Where is it stored?

Not inside a PHP file.

Not inside NGINX.

It is stored inside MariaDB.

WordPress sends an SQL query to MariaDB.

MariaDB saves the data.

Later,

when someone opens the page,

WordPress asks MariaDB for that post.

---

# Themes

A theme controls the appearance of the website.

Examples:

- Colors
- Fonts
- Layout
- Header
- Footer

Changing the theme changes how the website looks,

but not the content.

---

# Plugins

Plugins add new features to WordPress.

Examples:

- Contact forms
- SEO tools
- Security
- Backup
- Caching

Think of plugins as applications installed inside WordPress.

---

# Your Inception Project

Your WordPress container has one job.

Run PHP code.

Generate HTML pages.

Communicate with MariaDB.

It does **not** receive browser requests directly.

NGINX does that.

---

# Key Points

WordPress:

- is a Content Management System (CMS)
- is written in PHP
- stores data in MariaDB
- generates HTML pages
- depends on PHP-FPM to execute its code
- depends on NGINX to receive browser requests

---
B
## Chapter Summary

After reading this chapter, you should be able to answer:

- What is WordPress?
- What is a CMS?
- Is WordPress a web server?
- Is WordPress a database?
- Why does WordPress need PHP?
- Why does WordPress need MariaDB?
- What is `wp-config.php`?
- Where are blog posts stored?

If you cannot answer these questions, read this chapter again before moving to the next chapter.

---

➡️ **Next:** Chapter 15 — PHP-FPM
